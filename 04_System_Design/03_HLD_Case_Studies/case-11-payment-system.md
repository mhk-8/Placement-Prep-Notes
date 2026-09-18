# HLD — Payment System / Ledger

> The one case study where **correctness beats availability**, and where "eventually consistent" is the wrong answer. It is also the best place to demonstrate idempotency, sagas and double-entry accounting.

---

## 1. Requirements

**Functional**
- Charge a customer for an order, via an external payment provider.
- Record every movement of money in a ledger.
- Handle refunds, full and partial.
- Reconcile internal records against the provider's records.
- Report balances and transaction history.

**Non-functional**
- **Money must never be created or destroyed.** Every transaction balances.
- **No double charges**, ever — even when networks fail and clients retry.
- Strong consistency and durability; availability is secondary.
- Auditability: every state change is explainable after the fact.
- Regulatory: retain records for years; support dispute investigation.

---

## 2. Estimation

```
VOLUME    Say 10M transactions/day ÷ 10^5 s = 100 TPS      peak ×10 = 1,000 TPS
          (payments are extremely spiky — sales, paydays, festivals)

STORAGE   transaction record ~1 KB, ledger entries ~2 per transaction
          10M × 3 KB = 30 GB/day → 11 TB/year, retained 7 years ≈ 77 TB

READS     balance and history queries, perhaps 10× write volume = 1,000 QPS
```

**What the numbers decide — and this is the interesting part:** 1,000 TPS is *small*. A single well-tuned Postgres instance handles this comfortably. **So the design problem here is not scale at all; it is correctness.** Saying that explicitly is a strong move, because it prevents you from reflexively designing a distributed system where a transactional one is both simpler and safer.

---

## 3. The double-entry ledger

**Never store a single mutable `balance` column and update it.** That design loses history, makes auditing impossible, and turns every concurrent update into a lost-update race.

Instead, use **double-entry bookkeeping**: every transaction produces **two or more entries that sum to zero**.

```
Customer pays ₹1,000 for an order:

  ledger_entries
  ┌──────────────┬───────────────────┬──────────┬──────────────┐
  │ txn_id       │ account           │ amount   │ direction    │
  ├──────────────┼───────────────────┼──────────┼──────────────┤
  │ txn_abc      │ customer:123      │  -100000 │ DEBIT        │
  │ txn_abc      │ merchant:456      │  + 97000 │ CREDIT       │
  │ txn_abc      │ platform:fees     │  +  3000 │ CREDIT       │
  └──────────────┴───────────────────┴──────────┴──────────────┘
                                     sum = 0   ✔
```

**Properties this gives you:**

- **A balance is a derived value**, not stored state: `SELECT SUM(amount) FROM ledger_entries WHERE account = ?`. It can always be recomputed and therefore always verified.
- **The invariant is checkable**: `SUM(amount) GROUP BY txn_id` must be zero for every transaction, and the grand total across all accounts must be zero. A periodic job asserting this catches bugs that would otherwise be invisible.
- **Entries are append-only and immutable.** A correction is a new compensating entry, never an update — which is precisely what an audit requires.
- **A refund is not a deletion**; it is a new transaction with the opposite entries.

**Store money as integer minor units** (paise, cents). Floating point loses money, literally.

---

## 4. Idempotency — the non-negotiable mechanism

The failure this prevents: a client sends a charge, the network drops the **response**, the client retries, and the customer is charged twice. This is not an edge case — client timeouts are routine.

**The mechanism:**

1. The client generates a unique **idempotency key** per logical payment attempt, and sends it with the request.
2. The server attempts to insert the key into an `idempotency_keys` table **with a unique constraint**, in the same transaction as the work.
3. If the insert succeeds, this is the first attempt: do the work, store the response against the key.
4. If the insert fails on the unique constraint, this is a retry: **return the stored response** without re-executing.

```sql
CREATE TABLE idempotency_keys (
    key            VARCHAR(64) PRIMARY KEY,
    request_hash   VARCHAR(64) NOT NULL,   -- detect key reuse with a different body
    response_body  JSONB,
    status         VARCHAR(16),            -- IN_PROGRESS | COMPLETED
    created_at     TIMESTAMP NOT NULL
);
```

**Three details that separate a real answer from a sketch:**

- **The `request_hash`** catches a client reusing a key with a *different* payload, which is a client bug that must be reported rather than silently returning the wrong stored response.
- **The `IN_PROGRESS` status** handles a retry arriving while the first attempt is still running: return `409 Conflict` and let the client retry later, rather than executing twice concurrently.
- **The key must be inserted in the same transaction as the work**, or a crash between the two leaves them inconsistent.

---

## 5. Architecture

```
   Client / Order Service
        │ POST /v1/payments  (+ Idempotency-Key)
        ▼
   ┌────────────────────────┐
   │  Payment API           │  validate, idempotency check
   └───────────┬────────────┘
               ▼
   ┌────────────────────────┐        ┌──────────────────────┐
   │  Payment Orchestrator  │───────►│  Ledger Service      │
   │  (saga coordinator)    │        │  (append-only, ACID) │
   └───────────┬────────────┘        └──────────────────────┘
               │
               ▼
   ┌────────────────────────┐
   │  Provider Adapter      │───► Stripe / Razorpay / bank rails
   │  (per-PSP strategy)    │◄─── webhooks (async status)
   └───────────┬────────────┘
               ▼
   ┌────────────────────────┐        ┌──────────────────────┐
   │  Outbox → Kafka        │───────►│ Reconciliation job   │
   └────────────────────────┘        │ (daily, vs PSP file) │
                                     └──────────────────────┘
```

**The ledger is a single strongly-consistent store.** At 1,000 TPS this is one Postgres primary with synchronous replication — and choosing that deliberately, rather than a distributed store, is the right call.

**Provider adapters behind an interface** so a second payment provider (or a failover provider) is a new class. Payment providers have genuinely different APIs, error codes and webhook formats, so this abstraction earns its keep.

---

## 6. The payment saga

A payment spans systems that cannot share a transaction, so it is a **saga** with compensating actions.

```
  1. Create transaction (PENDING)          ─┐
  2. Reserve inventory                      │  compensate: release inventory
  3. Authorise with the payment provider    │  compensate: void the authorisation
  4. Write ledger entries                   │  compensate: write reversing entries
  5. Capture the payment                    │  compensate: refund
  6. Mark transaction COMPLETED            ─┘
```

**On failure at step N, run compensations for N−1 … 1 in reverse.**

**The states must be explicit and persisted**, because a crash between any two steps must be recoverable. A recovery process scans for transactions stuck in a non-terminal state past a timeout and either completes them or compensates.

**What a saga gives up:** atomicity and isolation. Intermediate states are visible — a customer may briefly see an authorisation before the order confirms. That must be acceptable to the product, and designing the compensations is the genuinely hard part. You cannot un-send a shipment; you issue a return.

---

## 7. Deep dives

### The external provider is unreliable — and authoritative
The provider may time out **after** having charged the card. You cannot distinguish "not charged" from "charged, response lost".

**Resolution:**
- Always send your own idempotency key to the provider; every serious PSP supports one.
- On timeout, **query the provider for the status of that key** rather than retrying blindly.
- Treat the provider's webhook as the authoritative status, and your own record as provisional until confirmed.
- **Webhooks arrive out of order and more than once.** Handle them idempotently, and use a sequence number or timestamp to ignore stale ones.

### Reconciliation
Every day, fetch the provider's settlement file and compare it with your ledger. Three classes of discrepancy:

| Discrepancy | Meaning | Action |
|---|---|---|
| In the provider's file, not in your ledger | You charged and did not record it | Investigate urgently; write the missing entries |
| In your ledger, not in the provider's file | You recorded a charge that did not happen | Reverse it |
| Amounts differ | Fees, currency conversion, partial capture | Usually explainable; reconcile the fee accounts |

**Reconciliation is not an optional extra — it is how you discover the bugs that idempotency and sagas missed.** Any payment design that omits it is incomplete, and saying so unprompted is a strong signal.

### Refunds
A refund is a **new transaction** with reversing ledger entries, linked to the original. Partial refunds sum to at most the original amount — an invariant enforced in the ledger, not in application code.

### Currency
Store the amount **and** the currency. Never sum across currencies. For conversion, record the rate used and the timestamp on the transaction, so the historical value is reconstructible.

### Auditability
Append-only entries, an immutable event log, and a recorded actor and reason for every state change. Corrections are compensating entries, never updates. This is a regulatory requirement in most jurisdictions and a debugging superpower in all of them.

---

## 8. Bottlenecks and failure modes

| Risk | Impact | Mitigation |
|---|---|---|
| Double charge | Direct financial and reputational harm | Idempotency keys end to end, including to the provider |
| Provider timeout ambiguity | Unknown charge state | Query by idempotency key; webhooks as the source of truth |
| Out-of-order webhooks | Stale status overwrites fresh | Sequence numbers; ignore older events |
| Saga stuck mid-flight | Money in limbo | Persisted states; a recovery job that completes or compensates |
| Ledger imbalance from a bug | Silent corruption | A continuous invariant check that every txn sums to zero |
| Hot account (the platform fee account) | Write contention on one row | The ledger is append-only, so there is **no hot row** — balances are derived, not updated. This is a designed-in benefit worth naming |
| Provider outage | Payments fail | A secondary provider behind the same adapter interface; queue and retry for non-urgent captures |

---

## 9. Trade-offs to state

| Choice | Alternative | Why |
|---|---|---|
| Single ACID ledger | Distributed store | 1,000 TPS does not need distribution, and correctness is the requirement |
| Double-entry, append-only | A mutable balance column | Auditability, checkable invariants, and no hot-row contention |
| Saga | Two-phase commit | 2PC across an external provider would block on coordinator failure |
| Idempotency keys everywhere | Retry and hope | Client timeouts are routine, and a double charge is unacceptable |
| Webhooks as authoritative | Trusting the synchronous response | The response can be lost after the charge succeeded |
| Integer minor units | Floating point | Floating point loses money |
| Daily reconciliation | Trusting the system | It is how you find the bugs the other mechanisms missed |

---

## 10. Practice

- [ ] Design from scratch in 45 minutes
- [ ] Write out the ledger entries for: a purchase with a fee, a partial refund, a chargeback
- [ ] Design the saga recovery job: how does it decide to complete or compensate?
- [ ] Design the webhook handler: idempotent, order-tolerant, replay-safe
- [ ] Argue, with numbers, why this system should **not** be distributed

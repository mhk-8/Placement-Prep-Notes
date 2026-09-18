# LLD Case Study — Splitwise (Expense Sharing)

> The modelling is straightforward; the interesting parts are the **split strategies** and the **debt simplification algorithm**, which is a genuine graph problem hiding inside an LLD question.

---

## 1. Requirements

**Functional**
- Users can be added to groups.
- An expense is recorded: who paid, how much, and how it is split among participants.
- Splits may be **equal**, **exact amounts**, or **percentages**.
- Show each user's balance: who owes whom, and how much.
- **Simplify debts** so the group settles with the fewest transactions.
- Record settlements (a payment between two users).

**Clarifying questions and assumptions**

| Question | Assumption |
|---|---|
| Multiple currencies? | Single currency; note where conversion would plug in |
| Can an expense span groups? | No — an expense belongs to one group, or to an ad-hoc set of users |
| Partial settlements? | Yes |
| Comments, receipts, notifications? | **Out of scope** |
| Precision? | Use integer minor units (paise) to avoid floating-point drift |

**Non-functional:** adding a new split type must not require editing existing classes; balances must always sum to zero.

---

## 2. The key modelling decisions

### Decision 1 — store pairwise balances, not a ledger of every expense

You could recompute balances by replaying every expense, but a group with 10,000 expenses would recompute on every view. Instead maintain a **balance sheet**: a map from `(debtor, creditor)` to amount, updated incrementally when an expense is added.

Keep the expenses too — they are the audit trail and allow recomputation — but serve reads from the balance sheet.

### Decision 2 — split type is a Strategy

Equal, exact and percentage splits differ only in **how the total is divided**. That is one varying behaviour, so it belongs behind an interface. A new split type (by shares, by weight) becomes a new class.

### Decision 3 — money as integer minor units

`0.1 + 0.2 != 0.3` in floating point, and an expense-sharing app that loses a paisa per split is broken. Store paise as `int`, and handle the remainder explicitly when a total does not divide evenly.

**This remainder handling is a detail interviewers notice:** ₹100 split three ways is 3333, 3333, 3334 — someone must absorb the extra paisa, and the design must say who.

---

## 3. Class diagram

```
   ┌──────────┐        ┌───────────┐
   │   User   │◄──────►│   Group   │      (many-to-many)
   └──────────┘        └─────┬─────┘
        ▲                    ◆
        │                    ▼
        │            ┌──────────────┐
        │            │   Expense    │
        │            └──────┬───────┘
        │                   ◆
        │                   ▼
        │            ┌──────────────┐        ┌──────────────────────┐
        └────────────│    Split     │        │ <<interface>>        │
                     │ (user, amount)│       │  SplitStrategy       │
                     └──────────────┘        └──────────────────────┘
                                                        △
                                        ┌───────────────┼───────────────┐
                                  EqualSplit      ExactSplit     PercentSplit

   ┌────────────────────┐        ┌─────────────────────────┐
   │  BalanceSheet      │        │ DebtSimplifier          │
   │ (pairwise net)     │        │ (greedy min-transactions)│
   └────────────────────┘        └─────────────────────────┘
```

---

## 4. Implementation

```python
from abc import ABC, abstractmethod
from collections import defaultdict
from dataclasses import dataclass, field
from enum import Enum
from typing import Dict, List, Tuple
import heapq
import uuid


Paise = int         # all money is integer minor units


@dataclass(frozen=True)
class User:
    id: str
    name: str


@dataclass(frozen=True)
class Split:
    user: User
    amount: Paise


# ---------- split strategies ----------

class SplitStrategy(ABC):
    @abstractmethod
    def split(self, total: Paise, participants: List[User], **kwargs) -> List[Split]:
        ...

    @staticmethod
    def _validate(total: Paise, splits: List[Split]) -> None:
        s = sum(x.amount for x in splits)
        if s != total:
            raise ValueError(f"splits sum to {s}, expected {total}")


class EqualSplit(SplitStrategy):
    def split(self, total, participants, **kwargs):
        n = len(participants)
        base, remainder = divmod(total, n)
        # The first `remainder` participants absorb one extra paisa each,
        # so the splits always sum exactly to the total.
        splits = [Split(u, base + (1 if i < remainder else 0))
                  for i, u in enumerate(participants)]
        self._validate(total, splits)
        return splits


class ExactSplit(SplitStrategy):
    def split(self, total, participants, amounts: List[Paise] = None, **kwargs):
        if amounts is None or len(amounts) != len(participants):
            raise ValueError("one amount required per participant")
        splits = [Split(u, a) for u, a in zip(participants, amounts)]
        self._validate(total, splits)         # must sum exactly to the total
        return splits


class PercentSplit(SplitStrategy):
    def split(self, total, participants, percents: List[float] = None, **kwargs):
        if percents is None or len(percents) != len(participants):
            raise ValueError("one percentage required per participant")
        if abs(sum(percents) - 100.0) > 1e-9:
            raise ValueError("percentages must sum to 100")
        amounts = [int(total * p / 100) for p in percents]
        amounts[-1] += total - sum(amounts)   # last participant absorbs rounding
        splits = [Split(u, a) for u, a in zip(participants, amounts)]
        self._validate(total, splits)
        return splits


# ---------- expense ----------

class Expense:
    def __init__(self, description: str, amount: Paise,
                 paid_by: User, splits: List[Split]):
        self.id = str(uuid.uuid4())
        self.description = description
        self.amount = amount
        self.paid_by = paid_by
        self.splits = splits


# ---------- balance sheet ----------

class BalanceSheet:
    # balances[a][b] = amount that a owes b (negative means b owes a)
    def __init__(self):
        self._b: Dict[str, Dict[str, Paise]] = defaultdict(lambda: defaultdict(int))

    def apply(self, expense: Expense) -> None:
        payer = expense.paid_by.id
        for s in expense.splits:
            if s.user.id == payer:
                continue                      # the payer does not owe themselves
            self._add(s.user.id, payer, s.amount)

    def settle(self, payer_id: str, payee_id: str, amount: Paise) -> None:
        self._add(payer_id, payee_id, -amount)

    def _add(self, debtor: str, creditor: str, amount: Paise) -> None:
        self._b[debtor][creditor] += amount
        self._b[creditor][debtor] -= amount    # keep the matrix antisymmetric

    def net(self, user_id: str) -> Paise:
        # Positive => this user is owed money overall.
        return -sum(self._b[user_id].values())

    def owed_by(self, user_id: str) -> Dict[str, Paise]:
        return {other: amt for other, amt in self._b[user_id].items() if amt > 0}

    def all_nets(self) -> Dict[str, Paise]:
        users = set(self._b) | {o for d in self._b.values() for o in d}
        return {u: self.net(u) for u in users}


# ---------- debt simplification ----------

class DebtSimplifier:
    # Greedy: repeatedly settle the largest creditor against the largest debtor.
    # Produces at most n-1 transactions, which is optimal in the common case
    # (finding the true minimum is NP-hard -- it is a partition problem).
    @staticmethod
    def simplify(nets: Dict[str, Paise]) -> List[Tuple[str, str, Paise]]:
        creditors = [(-amt, u) for u, amt in nets.items() if amt > 0]   # max-heap
        debtors = [(amt, u) for u, amt in nets.items() if amt < 0]      # min-heap
        heapq.heapify(creditors)
        heapq.heapify(debtors)

        transactions: List[Tuple[str, str, Paise]] = []
        while creditors and debtors:
            c_amt, creditor = heapq.heappop(creditors)
            d_amt, debtor = heapq.heappop(debtors)
            settle = min(-c_amt, -d_amt)
            transactions.append((debtor, creditor, settle))
            c_left, d_left = -c_amt - settle, -d_amt - settle
            if c_left > 0:
                heapq.heappush(creditors, (-c_left, creditor))
            if d_left > 0:
                heapq.heappush(debtors, (-d_left, debtor))
        return transactions


# ---------- service ----------

class ExpenseService:
    def __init__(self):
        self.sheet = BalanceSheet()
        self.expenses: List[Expense] = []

    def add_expense(self, description: str, amount: Paise, paid_by: User,
                    participants: List[User], strategy: SplitStrategy,
                    **kwargs) -> Expense:
        splits = strategy.split(amount, participants, **kwargs)
        expense = Expense(description, amount, paid_by, splits)
        self.sheet.apply(expense)
        self.expenses.append(expense)
        return expense

    def settle_up(self, payer: User, payee: User, amount: Paise) -> None:
        self.sheet.settle(payer.id, payee.id, amount)

    def simplify(self) -> List[Tuple[str, str, Paise]]:
        return DebtSimplifier.simplify(self.sheet.all_nets())
```

### Demonstration

```python
alice, bob, carol = User("a", "Alice"), User("b", "Bob"), User("c", "Carol")
svc = ExpenseService()

svc.add_expense("Dinner", 300_00, alice, [alice, bob, carol], EqualSplit())
svc.add_expense("Cab",    120_00, bob,   [bob, carol],        EqualSplit())

print(svc.sheet.all_nets())
# {'a': 20000, 'b': -4000, 'c': -16000}   (in paise: Alice is owed 200.00)

print(svc.simplify())
# [('c', 'a', 16000), ('b', 'a', 4000)]   two transactions instead of three
```

---

## 5. The debt simplification algorithm

This is the algorithmic content, and the part worth explaining carefully.

**The reduction.** Convert the pairwise debt graph into a single **net balance per person**. Alice owing Bob ₹50 while Bob owes Alice ₹30 is just Alice owing ₹20 — and once you net everything, the original edges no longer matter. The sum of all nets is always zero.

**The greedy algorithm.** Repeatedly take the largest creditor and the largest debtor and settle the smaller of the two magnitudes. Each transaction **zeroes out at least one person**, so with n people there are at most **n − 1** transactions.

**The honest caveat.** Minimising the number of transactions exactly is **NP-hard** — it reduces to a set-partition problem, because you would want to find subsets whose balances cancel exactly. The greedy result is optimal whenever no proper subset sums to zero, and near-optimal otherwise.

**Saying that caveat out loud is a strong move**: it shows you recognise the real complexity class rather than claiming a greedy heuristic is exact.

---

## 6. Design decisions

| Decision | Reason |
|---|---|
| Integer paise, never float | `0.1 + 0.2 != 0.3`; money must be exact |
| Explicit remainder handling in `EqualSplit` | ₹100 / 3 must still sum to ₹100 — someone absorbs the extra paisa |
| `_validate` on every strategy | Enforces the invariant that splits sum to the total, catching bugs at the boundary |
| Incremental `BalanceSheet` | O(1) balance reads rather than replaying every expense |
| Antisymmetric balance matrix | `balances[a][b] == -balances[b][a]` is an invariant the code maintains, so nets are always consistent |
| Expenses retained alongside balances | Audit trail and the ability to recompute if a bug corrupts the sheet |
| `SplitStrategy` interface | New split types are new classes — Open/Closed |
| `DebtSimplifier` as a separate class | It is a pure algorithm over nets; keeping it out of `BalanceSheet` respects SRP and makes it independently testable |

---

## 7. Follow-up questions

**"How do you handle multiple currencies?"**
Add a `currency` to `Money` and keep **one balance sheet per currency**, because netting across currencies requires a rate and rates change. Converting at settlement time (with the rate recorded on the settlement) is more honest than continuously re-valuing balances.

**"How do you handle an expense being edited or deleted?"**
Because the balance sheet is incremental, apply the **inverse** of the old expense and then the new one — the same technique as a reversing journal entry in accounting. Retaining the expense list makes this possible; if the sheet ever drifts, recompute from the expenses.

**"What if two people add expenses to the same group simultaneously?"**
Balance updates must be atomic. Single-process: a lock per group. Distributed: a transaction updating the affected pairwise rows, or an append-only event log per group with balances as a derived projection. The event-log version is attractive here because expense-sharing is naturally an audit-trail domain.

**"How do you scale to very large groups?"**
The pairwise matrix is O(n²) in the worst case. For large groups, store only **net per user** plus the expense log, and compute pairwise views on demand. Most real groups are small enough that this does not matter, and saying so is better than over-engineering.

**"How would you add recurring expenses?"**
A `RecurringExpense` template plus a scheduler that instantiates an `Expense` on each occurrence. The split strategy is reused unchanged — which is the payoff of having separated it.

---

## 8. Practice

- [ ] Redesign from scratch in 45 minutes
- [ ] Add a `ShareSplit` (by weights: 2 shares, 1 share, 1 share)
- [ ] Implement expense editing via inverse application
- [ ] Rewrite the balance sheet as an event log with a projection
- [ ] Prove that the greedy simplifier uses at most n − 1 transactions

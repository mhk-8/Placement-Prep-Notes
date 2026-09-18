# HLD — Ride Hailing (Uber / Ola)

> The case study whose distinguishing problem is **geospatial indexing**: efficiently answering "which drivers are near this rider?" over millions of continuously-moving points.

---

## 1. Requirements

**Functional**
- Drivers broadcast their location continuously.
- A rider requests a ride from A to B.
- The system matches a nearby available driver.
- Both parties track each other live during the trip.
- Fare calculation and payment at trip end.

**Non-functional**
- 1M active drivers, 100K concurrent trips.
- **Matching within a few seconds.**
- Location updates every 4 seconds per active driver.
- Correctness where it matters: **a driver must never be assigned two trips**.

---

## 2. Estimation

```
LOCATION UPDATES
  1M drivers ÷ 4 s                    = 250,000 writes/second   ← the big one
  each update ~100 B                  = 25 MB/s

MATCHING
  Say 1M rides/day ÷ 10^5 s           = 10 requests/second   peak ×5 = 50/s
  each searches a radius of drivers

TRIP TRACKING
  100K concurrent trips × 1 update/4s = 25,000 pushes/second to riders

STORAGE
  Trip records: 1M/day × 1 KB         = 1 GB/day  (trivial)
  Location history (if retained)      = 250K/s × 100 B = 2 TB/day (tier it)
```

**What the numbers decide:** 250,000 location writes per second against 50 match requests per second means the system is **overwhelmingly write-heavy on location** and the matching query is rare but latency-critical. That asymmetry shapes everything: location goes in memory, not in a durable database on the hot path.

---

## 3. The geospatial problem

**The naive approach:** store every driver's `(lat, lng)` and, on a match request, compute the distance to all of them. At 1 million drivers that is a full scan per request, and the distance formula is expensive. Unworkable.

**Two standard solutions:**

### Geohash
Encode `(lat, lng)` into a base32 string by recursively bisecting the space. **Nearby points share a common prefix**, so a proximity query becomes a **prefix match** — which any key-value store or index can do efficiently.

```
9q8yy    ~4.9 km × 4.9 km cell
9q8yyz   ~1.2 km × 0.6 km
9q8yyzk  ~153 m × 153 m
```

Precision is chosen by prefix length. Store drivers keyed by geohash prefix; a search fetches the rider's cell **and its 8 neighbours** — the neighbours matter, because a driver 50 metres away can be just across a cell boundary and would otherwise be invisible. Forgetting the neighbours is the classic bug in this design.

### S2 / H3
Google's S2 maps the sphere onto a quadtree of cells; Uber's H3 uses hexagons. **Hexagons have uniform distance to all neighbours**, which is why Uber chose them — with squares, diagonal neighbours are √2 further than orthogonal ones, which distorts distance-based logic.

**Either is a defensible answer.** What matters is recognising that this is a spatial indexing problem and naming the prefix/cell mechanism.

---

## 4. Architecture

```
   Drivers ──WebSocket──►┌──────────────────────┐
                         │ Location Service     │  250K writes/s
                         └──────────┬───────────┘
                                    ▼
                         ┌──────────────────────┐
                         │ Redis (geo index)    │  driver_id → cell
                         │ cell → {drivers}     │  in-memory, TTL'd
                         └──────────┬───────────┘
                                    │ query
   Riders ───HTTP───► ┌─────────────▼────────────┐
                      │   Matching Service       │
                      └─────────────┬────────────┘
                                    │ offer / accept
                      ┌─────────────▼────────────┐
                      │   Trip Service           │  state machine
                      └──┬────────────────┬──────┘
                         ▼                ▼
              ┌────────────────┐  ┌──────────────────┐
              │  Trip store    │  │ Kafka (events)   │
              │  (Postgres)    │  └────────┬─────────┘
              └────────────────┘           ▼
                                  ┌──────────────────┐
                                  │ Pricing, ETA,    │
                                  │ analytics, payments│
                                  └──────────────────┘
```

**Location state lives in Redis, not in a durable store.** It is ephemeral by nature (a location from 30 seconds ago is worthless), it is written 250,000 times a second, and losing it costs one update cycle. Writing it to Postgres would make the database the bottleneck for no benefit.

**Trip state lives in Postgres**, because a trip is a financial transaction with invariants.

---

## 5. The matching flow

1. Rider requests a ride with pickup and destination.
2. Matching Service computes the pickup cell and its neighbours.
3. Fetch candidate drivers from those cells — available, correct vehicle class, recently updated.
4. Rank by **ETA** (not straight-line distance — a driver across a river is close and unreachable), plus rating, acceptance rate and current utilisation.
5. Offer to the best candidate, with a short timeout (~15 s).
6. On accept, **atomically** transition both the driver and the trip; on decline or timeout, offer to the next.
7. Notify the rider, and begin live tracking.

**The correctness requirement:** a driver must never be assigned two trips. This is a conditional update, exactly as in the booking case study:
```sql
UPDATE drivers SET status='ASSIGNED', trip_id=:trip
 WHERE driver_id=:id AND status='AVAILABLE'
```
and check the affected row count. The read from Redis is advisory; **this write is the decision point.**

**Why offer-and-accept rather than direct assignment:** drivers decline, and a system that assigns unilaterally produces cancellations and a worse rider experience. The sequential offer loop with a timeout is what makes the match land.

---

## 6. Deep dives

### Location update path
250,000 writes/second is the highest-volume path. Keep it minimal: a WebSocket frame → validate → update two Redis structures (`driver → cell` and `cell → set of drivers`) with a TTL so a driver who goes offline disappears automatically. **No durable write on this path.** If location history is needed for analytics, emit to Kafka and let a consumer batch it into cold storage.

### ETA
Straight-line distance is wrong — roads, rivers, one-ways and traffic all matter. A routing service over a road graph, with live traffic as edge weights, gives real ETAs. **Cache ETAs between cell pairs**, because the same cell-to-cell estimate serves many requests and changes only as traffic changes.

### Surge pricing
Compute supply and demand **per cell per time window** from the event stream, and derive a multiplier. It is a stream-processing job, not a request-path computation. Note the feedback loop worth mentioning: surge attracts drivers, which reduces surge — so the multiplier must be smoothed or it oscillates.

### Trip state machine
```
REQUESTED → MATCHED → DRIVER_ARRIVING → IN_PROGRESS → COMPLETED
     │          │              │              │
     └──────────┴──────────────┴──────────────┴──► CANCELLED
```
Transitions are guarded — you cannot complete a trip that was never started. This is the State pattern from `02_LLD_OOD`, at service scale.

### Payment
A **saga**, not a distributed transaction: authorise at trip start, capture at completion, compensate (refund) on cancellation. Payment failure must not lose the trip record, and the trip must not be held open waiting for a payment gateway.

---

## 7. Bottlenecks and failure modes

| Risk | Impact | Mitigation |
|---|---|---|
| Location write volume | Redis saturation | Shard the geo index by region; it is naturally partitionable by geography |
| Hot cell (airport, stadium, concert end) | One shard saturates | Sub-divide hot cells to a finer precision dynamically |
| Redis failure | Matching stops | Replicas; and location rebuilds within one update cycle (4 s) — a genuinely benign failure |
| Double assignment | A driver gets two trips | Conditional update with row-count check |
| Matching latency | Riders abandon | Cap the candidate set; precompute ETAs per cell pair |
| Regional outage | A whole city stops | Partition by region so failures are geographically contained |

**The nice property of this system:** the highest-volume data is the least valuable. Losing the entire location index costs four seconds. That is worth saying explicitly, because it justifies the in-memory choice.

---

## 8. Trade-offs to state

| Choice | Alternative | Why |
|---|---|---|
| Geohash/H3 cells | Distance to all drivers | A full scan per match at 1M drivers is unworkable |
| Redis for location | A durable database | 250K writes/s of data that is worthless after 30 seconds |
| Postgres for trips | A NoSQL store | Trips are financial records with invariants and moderate volume |
| Offer-and-accept | Direct assignment | Drivers decline; unilateral assignment produces cancellations |
| ETA-based ranking | Straight-line distance | A driver across a river is near and unreachable |
| Saga for payment | Distributed transaction | 2PC across a payment gateway would block |
| Regional partitioning | One global cluster | Contains blast radius, and the workload is inherently local |

---

## 9. Practice

- [ ] Design from scratch in 45 minutes, deriving the 250K writes/second
- [ ] Work out the geohash precision that gives roughly 1 km cells and why neighbours are needed
- [ ] Design the hot-cell subdivision for a stadium at closing time
- [ ] Add ride pooling — what changes in matching and pricing?
- [ ] Design the surge-pricing stream job, including the oscillation damping

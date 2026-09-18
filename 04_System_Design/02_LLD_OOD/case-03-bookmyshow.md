# LLD Case Study — Movie Ticket Booking (BookMyShow)

> The concurrency problem in this one is the whole point: **two users must never book the same seat.** Everything else is ordinary modelling, and an answer that designs beautiful classes while hand-waving the double-booking will not pass.

---

## 1. Requirements

**Functional**
- Browse cities → cinemas → movies → shows.
- View the seat map for a show, with availability.
- Select seats, **hold** them temporarily, pay, and receive a booking.
- Cancel a booking (with a refund policy).

**Clarifying questions and assumptions**

| Question | Assumption |
|---|---|
| Seat types and pricing? | Silver / Gold / Platinum, priced per show |
| How long is a seat held before payment? | **10 minutes**, then released automatically |
| Can a user book multiple seats at once? | Yes, atomically — all or none |
| Payment? | An external gateway behind an interface |
| Concurrency scale? | Many users hitting a popular show's seat map simultaneously |
| Seat recommendations, food, offers? | **Out of scope** |

**Non-functional:** seat allocation must be correct under concurrency; adding a pricing rule or a payment method must not require editing existing classes.

---

## 2. Entity model

```
City ──< Cinema ──< Screen ──< Seat
                      │
                      └──< Show ──< ShowSeat ──> Booking ──> Payment
                            │
                          Movie
```

**The crucial distinction — `Seat` versus `ShowSeat`:**

- **`Seat`** is *physical*: row A, number 12, in screen 3. It exists whether or not anything is showing.
- **`ShowSeat`** is the *bookable unit*: seat A12 **for the 9 pm show on Friday**. It carries the status (available / held / booked) and the price.

Conflating them is the single most common modelling error in this problem — you end up unable to represent the same physical seat being free at 6 pm and booked at 9 pm.

---

## 3. Class diagram

```
   ┌──────────┐   ┌───────────┐   ┌──────────┐   ┌────────┐
   │   City   │◇──│  Cinema   │◆──│  Screen  │◆──│  Seat  │
   └──────────┘   └───────────┘   └────┬─────┘   └────────┘
                                       │                ▲
                                  ┌────▼─────┐          │ refers to
                                  │   Show   │          │
                                  └────┬─────┘     ┌────┴────────┐
                                  ◆    │          │  ShowSeat    │
                                       └─────────►│ (status,price)│
   ┌──────────┐                                   └───────┬──────┘
   │  Movie   │◄────── Show                               │
   └──────────┘                                           │
                     ┌─────────────────┐                  │
                     │ BookingService  │──────────────────┘
                     └────────┬────────┘
                              │ uses
              ┌───────────────┼────────────────┐
              ▼               ▼                ▼
   ┌────────────────┐ ┌───────────────┐ ┌──────────────────┐
   │ <<interface>>  │ │ <<interface>> │ │ <<interface>>    │
   │ SeatLockProvider│ │PaymentGateway │ │ PricingStrategy  │
   └────────────────┘ └───────────────┘ └──────────────────┘

   Booking ──► BookingStatus (enum: HELD, CONFIRMED, CANCELLED, EXPIRED)
```

---

## 4. Implementation

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass, field
from datetime import datetime, timedelta
from enum import Enum
from threading import RLock
from typing import Dict, List, Optional, Set
import uuid


class SeatType(Enum):
    SILVER = 1
    GOLD = 2
    PLATINUM = 3


class SeatStatus(Enum):
    AVAILABLE = 1
    HELD = 2
    BOOKED = 3


class BookingStatus(Enum):
    HELD = 1
    CONFIRMED = 2
    CANCELLED = 3
    EXPIRED = 4


@dataclass(frozen=True)
class Money:
    amount: float
    currency: str = "INR"
    def __add__(self, o): return Money(self.amount + o.amount, self.currency)


# ---------- physical layout ----------

@dataclass(frozen=True)
class Seat:
    id: str
    row: str
    number: int
    type: SeatType


class Screen:
    def __init__(self, screen_id: str, seats: List[Seat]):
        self.id = screen_id
        self.seats = seats


@dataclass
class Movie:
    id: str
    title: str
    duration_min: int
    language: str


# ---------- the bookable unit ----------

class ShowSeat:
    def __init__(self, seat: Seat, price: Money):
        self.seat = seat
        self.price = price
        self.status = SeatStatus.AVAILABLE
        self.held_by: Optional[str] = None
        self.held_until: Optional[datetime] = None

    def is_free(self, now: datetime) -> bool:
        if self.status is SeatStatus.AVAILABLE:
            return True
        if self.status is SeatStatus.HELD and self.held_until and now > self.held_until:
            return True                 # the hold has lapsed
        return False


class Show:
    def __init__(self, show_id: str, movie: Movie, screen: Screen,
                 start: datetime, pricing: "PricingStrategy"):
        self.id = show_id
        self.movie = movie
        self.screen = screen
        self.start = start
        self.seats: Dict[str, ShowSeat] = {
            s.id: ShowSeat(s, pricing.price(s.type, start)) for s in screen.seats
        }
        self._lock = RLock()            # guards all seat transitions for this show

    def available(self, now: datetime) -> List[ShowSeat]:
        with self._lock:
            return [ss for ss in self.seats.values() if ss.is_free(now)]


# ---------- pricing (Strategy) ----------

class PricingStrategy(ABC):
    @abstractmethod
    def price(self, stype: SeatType, show_start: datetime) -> Money: ...


class BasePricing(PricingStrategy):
    RATES = {SeatType.SILVER: 150.0, SeatType.GOLD: 250.0, SeatType.PLATINUM: 400.0}
    def price(self, stype, show_start): return Money(self.RATES[stype])


class WeekendSurcharge(PricingStrategy):
    # Decorator: composes with any base pricing.
    def __init__(self, base: PricingStrategy, multiplier: float = 1.3):
        self._base, self._m = base, multiplier
    def price(self, stype, show_start):
        p = self._base.price(stype, show_start)
        return Money(p.amount * self._m) if show_start.weekday() >= 5 else p


# ---------- payment ----------

class PaymentStatus(Enum):
    SUCCESS = 1
    FAILED = 2


class PaymentGateway(ABC):
    @abstractmethod
    def charge(self, user_id: str, amount: Money, idempotency_key: str) -> PaymentStatus: ...


class MockGateway(PaymentGateway):
    def charge(self, user_id, amount, idempotency_key):
        return PaymentStatus.SUCCESS


# ---------- booking ----------

class Booking:
    HOLD_MINUTES = 10

    def __init__(self, user_id: str, show: Show, seats: List[ShowSeat]):
        self.id = str(uuid.uuid4())
        self.user_id = user_id
        self.show = show
        self.seats = seats
        self.created = datetime.now()
        self.expires = self.created + timedelta(minutes=self.HOLD_MINUTES)
        self.status = BookingStatus.HELD
        self.total = Money(sum(s.price.amount for s in seats))


class SeatUnavailable(Exception):
    pass


class BookingService:
    def __init__(self, gateway: PaymentGateway):
        self._gateway = gateway
        self._bookings: Dict[str, Booking] = {}

    # ---- the critical section ----
    def hold_seats(self, user_id: str, show: Show, seat_ids: List[str]) -> Booking:
        now = datetime.now()
        with show._lock:                       # one lock per show, not global
            chosen = []
            for sid in seat_ids:
                ss = show.seats.get(sid)
                if ss is None:
                    raise SeatUnavailable(f"no such seat {sid}")
                if not ss.is_free(now):
                    raise SeatUnavailable(f"seat {sid} is not available")
                chosen.append(ss)
            # All checks passed; commit the hold atomically.
            booking = Booking(user_id, show, chosen)
            for ss in chosen:
                ss.status = SeatStatus.HELD
                ss.held_by = booking.id
                ss.held_until = booking.expires
        self._bookings[booking.id] = booking
        return booking

    def confirm(self, booking_id: str) -> Booking:
        booking = self._bookings[booking_id]
        show = booking.show
        with show._lock:
            if booking.status is not BookingStatus.HELD:
                raise ValueError(f"booking is {booking.status.name}")
            if datetime.now() > booking.expires:
                self._release(booking, BookingStatus.EXPIRED)
                raise SeatUnavailable("hold expired")
            result = self._gateway.charge(booking.user_id, booking.total, booking.id)
            if result is not PaymentStatus.SUCCESS:
                self._release(booking, BookingStatus.CANCELLED)
                raise ValueError("payment failed")
            for ss in booking.seats:
                ss.status = SeatStatus.BOOKED
                ss.held_until = None
            booking.status = BookingStatus.CONFIRMED
        return booking

    def cancel(self, booking_id: str) -> None:
        booking = self._bookings[booking_id]
        with booking.show._lock:
            self._release(booking, BookingStatus.CANCELLED)

    def _release(self, booking: Booking, status: BookingStatus) -> None:
        for ss in booking.seats:
            if ss.held_by == booking.id:
                ss.status = SeatStatus.AVAILABLE
                ss.held_by = None
                ss.held_until = None
        booking.status = status

    def expire_stale_holds(self, show: Show) -> None:
        # Run periodically; the is_free() lapse check makes this a cleanup,
        # not a correctness requirement.
        now = datetime.now()
        with show._lock:
            for ss in show.seats.values():
                if ss.status is SeatStatus.HELD and ss.held_until and now > ss.held_until:
                    ss.status = SeatStatus.AVAILABLE
                    ss.held_by = None
                    ss.held_until = None
```

---

## 5. The concurrency discussion — the heart of this problem

### In a single process
One `RLock` **per show**, not a global lock. Popular shows are the contended ones, and locking per show means booking for different films never contends. The critical section checks *all* requested seats and only then commits, so a multi-seat booking is atomic — you never end up holding two of three seats.

### In a distributed system
A process-level lock is worthless across servers. Three real options, in increasing order of strength:

**1. Optimistic concurrency (the usual answer).**
```sql
UPDATE show_seats
   SET status = 'HELD', held_by = :booking, held_until = :expiry
 WHERE show_id = :show AND seat_id IN (:seats)
   AND (status = 'AVAILABLE'
        OR (status = 'HELD' AND held_until < NOW()))
```
Then check the **affected row count**: if it does not equal the number of seats requested, someone else won — roll back the transaction and tell the user. No locks are held across a user's thinking time, and the database's own row locks provide the atomicity.

**This is the answer to give**, because it is what real systems do and it requires no additional infrastructure.

**2. A distributed lock (Redis).** `SET lock:show:seat NX PX 600000` per seat. Works, but adds a dependency and the usual distributed-lock caveats: a lock holder that pauses (GC, network) can have its lease expire while it believes it still holds the lock, so the underlying write must still be conditional. Mention that caveat — it shows you know distributed locks are not a complete solution.

**3. Serialise per show through a single writer.** Route all bookings for a show to one partition or one actor (by hashing `show_id`), so there is no concurrency to resolve. Elegant, and it makes that partition a hot spot for a blockbuster release.

### Why holds expire
Without a time-bounded hold, a user who abandons checkout locks seats forever. With it, the seat returns automatically. **Note the design detail:** `is_free()` treats a lapsed hold as available, so correctness does not depend on the cleanup job running promptly — the sweeper is an optimisation for the seat map's accuracy, not a correctness requirement. That separation is worth pointing out.

---

## 6. Design decisions

| Decision | Reason |
|---|---|
| `Seat` vs `ShowSeat` split | The same physical seat has independent availability per show |
| Lock **per show** | Contention is per show; a global lock would serialise the whole system |
| Check-all-then-commit inside the lock | A multi-seat booking must be all-or-nothing |
| Hold with an expiry, and `is_free` honours lapses | Abandoned checkouts self-heal without a reliable sweeper |
| `PricingStrategy` + decorator | Weekend, matinee and promotional pricing compose |
| Idempotency key on `charge` (the booking id) | A retried payment after a timeout must not double-charge |
| `PaymentGateway` as an interface | Dependency inversion — testable without a real gateway |
| `BookingStatus` as a state enum with guarded transitions | `confirm` on a cancelled booking raises rather than silently succeeding |

---

## 7. Follow-up questions

**"Two users select the same seat at the same instant. Walk me through it."**
Both read the seat map and see it free — that read is not authoritative. Both attempt the conditional update; the database applies row locks and one update matches zero rows. That user gets an immediate "seat just went" and the map refreshes. **The read is advisory; the conditional write is the decision point.**

**"How do you stop a bot holding every seat?"**
Rate limit holds per user and per IP, cap concurrent holds per user, require authentication for holds, and shorten the hold window for unauthenticated sessions. It is a rate-limiting problem, not a locking one.

**"How would you show a live seat map to thousands of users?"**
Push updates over WebSocket or server-sent events from a per-show channel, with the seat map itself cached and invalidated on change. Accept a second of staleness in the display — the conditional write is what actually prevents double-booking, so a stale map costs a retry, not correctness.

**"How do refunds and cancellation policies work?"**
A `RefundPolicy` strategy taking the booking and the current time, returning the refundable amount (100% until 24 hours before, 50% until 2 hours, 0 after). A new policy is a new class.

**"How would you handle a blockbuster release?"**
That show's partition becomes a hot spot. Options: a virtual waiting room admitting users at a controlled rate; pre-generating the seat map in a cache; and sharding by show so the hot show does not affect others. This is the same hot-partition discussion as `01_Fundamentals/04`.

---

## 8. Practice

- [ ] Redesign from scratch in 45 minutes
- [ ] Add group booking with an "adjacent seats only" constraint
- [ ] Add a `RefundPolicy` strategy and a cancellation flow
- [ ] Rewrite `hold_seats` as the SQL conditional update and handle the partial-match case
- [ ] Add a waiting room for high-demand shows

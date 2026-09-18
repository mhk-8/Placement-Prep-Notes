# LLD Case Study — Parking Lot

> The most frequently asked LLD problem. Work it fully once and most of the machinery transfers to the others.

---

## 1. Requirements (what to ask, and the answers assumed here)

**Functional**
- A vehicle enters through an entry gate and receives a ticket.
- The system allocates an appropriate free spot.
- On exit, the fee is computed from the duration and the spot type, and is paid.
- Displays show free spot counts per floor and type.

**Clarifying questions and assumed answers**

| Question | Assumption |
|---|---|
| Vehicle types? | Motorcycle, Car, Truck — needing Small, Medium, Large spots |
| Can a small vehicle use a bigger spot? | Yes, if none of its own size is free |
| Multiple floors? | Yes |
| Multiple gates? | Yes — several entry and exit gates, operating concurrently |
| Pricing? | Per hour, varying by spot type; must support weekend/holiday rates later |
| Payment? | Cash and card |
| Reservations? | **Out of scope** |
| Persistence? | **Out of scope** — in-memory |

**Non-functional:** concurrent gates must never allocate the same spot twice; adding a vehicle type or a pricing scheme must not require editing existing classes.

---

## 2. Entities

Nouns → candidate classes: Vehicle, ParkingSpot, Floor, ParkingLot, Ticket, EntryGate, ExitGate, Payment, PricingStrategy, Display.

Filtering:

| Concept | Kind | Why |
|---|---|---|
| VehicleType, SpotType, TicketStatus | **enum** | fixed set of values |
| Vehicle | class | has identity (a registration number) |
| ParkingSpot | class | has state (free/occupied) |
| Floor | class | **owns** its spots |
| ParkingLot | class | owns floors; the aggregate root |
| Ticket | class | has identity and lifecycle |
| EntryGate / ExitGate | class | behaviour + identity |
| PricingStrategy | **interface** | the behaviour that varies |
| PaymentMethod | **interface** | the behaviour that varies |
| SpotAllocationStrategy | **interface** | allocation policy may vary |
| "free spot count" | **not an entity** | derived |

---

## 3. Class diagram

```
                       ┌──────────────┐
                       │  ParkingLot  │  (aggregate root)
                       └──────┬───────┘
                      ◆       │        ◆
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
        ┌──────────┐   ┌────────────┐   ┌───────────┐
        │  Floor   │   │ EntryGate  │   │ ExitGate  │
        └────┬─────┘   └─────┬──────┘   └─────┬─────┘
             ◆               │                │
             ▼               │                │
      ┌──────────────┐       │                │
      │ ParkingSpot  │       ▼                ▼
      └──────────────┘   ┌────────────────────────┐
             ▲           │    TicketService       │
             │           └────────────┬───────────┘
             │ allocates              │ uses
   ┌─────────┴──────────┐             ▼
   │ <<interface>>      │   ┌───────────────────────┐
   │ SpotAllocation     │   │ <<interface>>         │
   │ Strategy           │   │ PricingStrategy       │
   └────────────────────┘   └───────────────────────┘
                                       △
                          ┌────────────┼────────────┐
                    HourlyPricing  WeekendPricing  FlatPricing

   ┌──────────────┐        ┌────────────────────┐
   │   Ticket     │───────►│ <<interface>>      │
   └──────────────┘        │ PaymentMethod      │
                           └────────────────────┘
                                    △
                          ┌─────────┴─────────┐
                     CashPayment        CardPayment
```

**Relationships:** `ParkingLot ◆── Floor ◆── ParkingSpot` are **composition** (destroy the lot and the spots are meaningless). `ParkingSpot ◇── Vehicle` is **aggregation** (a vehicle exists independently).

---

## 4. Implementation

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass, field
from datetime import datetime
from enum import Enum
from threading import Lock
from typing import Optional, List, Dict
import uuid


# ---------- value types ----------

class VehicleType(Enum):
    MOTORCYCLE = 1
    CAR = 2
    TRUCK = 3


class SpotType(Enum):
    SMALL = 1
    MEDIUM = 2
    LARGE = 3

    @staticmethod
    def fits(vehicle: VehicleType) -> List["SpotType"]:
        # Preference order: exact size first, then any larger spot.
        order = {
            VehicleType.MOTORCYCLE: [SpotType.SMALL, SpotType.MEDIUM, SpotType.LARGE],
            VehicleType.CAR:        [SpotType.MEDIUM, SpotType.LARGE],
            VehicleType.TRUCK:      [SpotType.LARGE],
        }
        return order[vehicle]


class TicketStatus(Enum):
    ACTIVE = 1
    PAID = 2
    CLOSED = 3


@dataclass(frozen=True)
class Money:
    amount: float
    currency: str = "INR"

    def __add__(self, other: "Money") -> "Money":
        assert self.currency == other.currency
        return Money(self.amount + other.amount, self.currency)


@dataclass
class Vehicle:
    registration: str
    vtype: VehicleType


# ---------- spots and floors ----------

class ParkingSpot:
    def __init__(self, spot_id: str, stype: SpotType):
        self.id = spot_id
        self.type = stype
        self._vehicle: Optional[Vehicle] = None
        self._lock = Lock()

    @property
    def is_free(self) -> bool:
        return self._vehicle is None

    def try_occupy(self, vehicle: Vehicle) -> bool:
        # Atomic check-and-set: two gates cannot both take this spot.
        with self._lock:
            if self._vehicle is not None:
                return False
            self._vehicle = vehicle
            return True

    def release(self) -> None:
        with self._lock:
            self._vehicle = None


class Floor:
    def __init__(self, number: int, spots: List[ParkingSpot]):
        self.number = number
        self._spots = spots

    def free_spots(self, stype: SpotType) -> List[ParkingSpot]:
        return [s for s in self._spots if s.type == stype and s.is_free]

    def free_count(self, stype: SpotType) -> int:
        return len(self.free_spots(stype))


# ---------- allocation strategy (Strategy pattern) ----------

class SpotAllocationStrategy(ABC):
    @abstractmethod
    def allocate(self, floors: List[Floor], vehicle: Vehicle) -> Optional[ParkingSpot]:
        ...


class NearestFirstAllocation(SpotAllocationStrategy):
    # Lowest floor, then exact size before larger sizes.
    def allocate(self, floors, vehicle):
        for stype in SpotType.fits(vehicle.vtype):
            for floor in sorted(floors, key=lambda f: f.number):
                for spot in floor.free_spots(stype):
                    if spot.try_occupy(vehicle):      # may lose the race; try the next
                        return spot
        return None


# ---------- pricing (Strategy pattern) ----------

class PricingStrategy(ABC):
    @abstractmethod
    def price(self, stype: SpotType, entry: datetime, exit_: datetime) -> Money:
        ...


class HourlyPricing(PricingStrategy):
    RATES = {SpotType.SMALL: 20.0, SpotType.MEDIUM: 40.0, SpotType.LARGE: 60.0}

    def price(self, stype, entry, exit_):
        hours = max(1, -(-int((exit_ - entry).total_seconds()) // 3600))  # ceil, min 1
        return Money(hours * self.RATES[stype])


class WeekendPricing(PricingStrategy):
    # Decorator-style composition: wraps any base strategy.
    def __init__(self, base: PricingStrategy, multiplier: float = 1.5):
        self._base = base
        self._multiplier = multiplier

    def price(self, stype, entry, exit_):
        base = self._base.price(stype, entry, exit_)
        if entry.weekday() >= 5:
            return Money(base.amount * self._multiplier, base.currency)
        return base


# ---------- payment (Strategy pattern) ----------

class PaymentMethod(ABC):
    @abstractmethod
    def pay(self, amount: Money) -> bool:
        ...


class CashPayment(PaymentMethod):
    def pay(self, amount): return True


class CardPayment(PaymentMethod):
    def __init__(self, card_token: str):
        self._token = card_token

    def pay(self, amount):
        return True     # would call a gateway


# ---------- ticket ----------

class Ticket:
    def __init__(self, vehicle: Vehicle, spot: ParkingSpot):
        self.id = str(uuid.uuid4())
        self.vehicle = vehicle
        self.spot = spot
        self.entry_time = datetime.now()
        self.exit_time: Optional[datetime] = None
        self.amount: Optional[Money] = None
        self.status = TicketStatus.ACTIVE

    def close(self, exit_time: datetime, amount: Money) -> None:
        if self.status is not TicketStatus.ACTIVE:
            raise ValueError(f"ticket {self.id} is already {self.status.name}")
        self.exit_time = exit_time
        self.amount = amount
        self.status = TicketStatus.PAID


# ---------- gates ----------

class EntryGate:
    def __init__(self, gate_id: str, lot: "ParkingLot"):
        self.id = gate_id
        self._lot = lot

    def admit(self, vehicle: Vehicle) -> Ticket:
        return self._lot.park(vehicle)


class ExitGate:
    def __init__(self, gate_id: str, lot: "ParkingLot"):
        self.id = gate_id
        self._lot = lot

    def process(self, ticket: Ticket, method: PaymentMethod) -> Money:
        return self._lot.unpark(ticket, method)


# ---------- the aggregate root ----------

class ParkingLotFull(Exception):
    pass


class ParkingLot:
    def __init__(self, floors: List[Floor],
                 allocator: SpotAllocationStrategy,
                 pricing: PricingStrategy):
        self._floors = floors
        self._allocator = allocator
        self._pricing = pricing
        self._active: Dict[str, Ticket] = {}
        self._lock = Lock()

    def park(self, vehicle: Vehicle) -> Ticket:
        spot = self._allocator.allocate(self._floors, vehicle)
        if spot is None:
            raise ParkingLotFull(f"no spot for {vehicle.vtype.name}")
        ticket = Ticket(vehicle, spot)
        with self._lock:
            self._active[ticket.id] = ticket
        return ticket

    def unpark(self, ticket: Ticket, method: PaymentMethod) -> Money:
        with self._lock:
            if ticket.id not in self._active:
                raise ValueError("unknown or already-closed ticket")
        exit_time = datetime.now()
        amount = self._pricing.price(ticket.spot.type, ticket.entry_time, exit_time)
        if not method.pay(amount):
            raise ValueError("payment failed")
        ticket.close(exit_time, amount)
        ticket.spot.release()
        with self._lock:
            del self._active[ticket.id]
        return amount

    def availability(self) -> Dict[int, Dict[str, int]]:
        return {f.number: {st.name: f.free_count(st) for st in SpotType}
                for f in self._floors}
```

### Demonstration

```python
floors = [
    Floor(1, [ParkingSpot(f"1-{i}", SpotType.SMALL) for i in range(2)] +
             [ParkingSpot(f"1-M{i}", SpotType.MEDIUM) for i in range(3)]),
    Floor(2, [ParkingSpot(f"2-L{i}", SpotType.LARGE) for i in range(2)]),
]

lot = ParkingLot(floors, NearestFirstAllocation(),
                 WeekendPricing(HourlyPricing()))
entry, exit_gate = EntryGate("E1", lot), ExitGate("X1", lot)

ticket = entry.admit(Vehicle("KA-01-HH-1234", VehicleType.CAR))
print(ticket.spot.id, lot.availability())
print(exit_gate.process(ticket, CardPayment("tok_123")))
```

---

## 5. Design decisions, and why

| Decision | Reason |
|---|---|
| `PricingStrategy` as an interface | Open/Closed — weekend pricing is a new class, not an edit |
| `WeekendPricing` **wraps** a base strategy | Decorator: rates and modifiers compose independently |
| `SpotAllocationStrategy` extracted | "nearest first" may become "cheapest first" or "by EV charger" |
| Spot occupancy via `try_occupy` under a lock | Two gates must never allocate the same spot — an atomic check-and-set |
| `SpotType.fits()` returns a preference **list** | Encodes "a car may take a large spot if no medium is free" declaratively, with no `if` chain |
| `Money` is a frozen dataclass | Prevents float amounts being mutated; currency is explicit |
| `Ticket.close` rejects a non-active ticket | An invariant enforced by the object, not by callers |
| `ParkingLot` is the aggregate root | Gates do not reach into floors and spots; one entry point for the invariants |

---

## 6. The minute-35 extension: electric vehicles

**Requirement:** "Now support electric vehicles that must park in spots with a charger, and are billed for electricity as well as time."

The design absorbs this **additively**:

```python
class VehicleType(Enum):
    MOTORCYCLE = 1
    CAR = 2
    TRUCK = 3
    ELECTRIC_CAR = 4          # new value

class SpotType(Enum):
    ...
    ELECTRIC = 4              # new value
    # and ELECTRIC_CAR's fits() list becomes [ELECTRIC, MEDIUM, LARGE]

class ChargingPricing(PricingStrategy):     # new class, nothing edited
    def __init__(self, base: PricingStrategy, per_kwh: float):
        self._base, self._per_kwh = base, per_kwh

    def price(self, stype, entry, exit_):
        base = self._base.price(stype, entry, exit_)
        if stype is SpotType.ELECTRIC:
            hours = (exit_ - entry).total_seconds() / 3600
            return base + Money(hours * 7.0 * self._per_kwh)    # 7 kW charger
        return base
```

**No existing class is modified** except the two enums and the `fits` table, which is the one place type knowledge is centralised deliberately. `ChargingPricing` composes with `WeekendPricing` in any order.

**Say this out loud:** "Because pricing is a strategy and allocation is a strategy, the new requirement is two new classes and two enum values. Nothing that currently works has to change."

---

## 7. Follow-up questions and answers

**"Two cars arrive at different gates simultaneously and only one spot is free."**
`try_occupy` performs an atomic check-and-set under the spot's own lock, so exactly one succeeds; the loser's allocator simply continues to the next candidate spot. Locking per spot rather than globally keeps contention low. In a distributed version this becomes a conditional write (`UPDATE spots SET vehicle=? WHERE id=? AND vehicle IS NULL`) and the row count tells you who won.

**"How do displays stay current?"**
Observer: `ParkingSpot` publishes an occupancy-changed event; `Display` objects subscribe and update their counts. That keeps `ParkingSpot` unaware of displays, so adding a mobile app is another subscriber.

**"How would you add reservations?"**
A `Reservation` entity holding a spot for a time window, and a `SpotAllocationStrategy` that skips reserved spots outside their window. Notably this needs **no change to pricing or gates**.

**"How would you support multiple parking lots?"**
`ParkingLot` is already the aggregate root, so a `ParkingLotRegistry` above it is enough. The interesting change is that `Ticket` ids must become globally unique — already true, since they are UUIDs.

**"Where would persistence go?"**
Behind repository interfaces (`TicketRepository`, `SpotRepository`) injected into `ParkingLot` — Dependency Inversion, so the domain does not depend on the database. In memory today, Postgres tomorrow, with no domain change.

---

## 8. What to say while designing this

> "The two things I expect to vary are pricing and allocation, so both go behind interfaces — that's Open/Closed, and it's what makes the electric-vehicle requirement additive later."

> "I'm putting the occupancy check-and-set inside `ParkingSpot` under its own lock, because two gates running concurrently is the one genuine race in this system."

> "`ParkingLot` is the aggregate root: gates talk to it rather than reaching into floors and spots, so the invariants live in one place."

---

## 9. Practice

- [ ] Redesign from scratch in 45 minutes without looking
- [ ] Add: monthly pass holders who skip payment
- [ ] Add: valet parking, where staff park the vehicle and the spot is chosen later
- [ ] Add: multiple entrances with per-gate queues and fair allocation
- [ ] Convert the concurrency handling to a database-backed conditional update

# LLD Case Study — Elevator System

> The best problem for demonstrating the **State pattern** and a non-trivial scheduling decision. Also the one where candidates most often produce a god class.

---

## 1. Requirements

**Functional**
- A building has multiple floors and multiple elevator cars.
- **External request:** a person on floor N presses UP or DOWN.
- **Internal request:** a person inside a car presses a destination floor.
- The system assigns an external request to a car and the car services its requests efficiently.
- Doors open and close; the car reports its current floor and direction.

**Clarifying questions and assumptions**

| Question | Assumption |
|---|---|
| How many cars? | Several — assignment is part of the problem |
| Should the algorithm be optimal? | No — a reasonable, explainable policy (SCAN/"elevator algorithm") |
| Capacity limits? | Track a weight/person limit; reject boarding beyond it |
| Emergency, maintenance, fire mode? | Model as states; implement one (maintenance) |
| Express elevators / floor restrictions? | **Out of scope**, but mention how it plugs in |

**Non-functional:** adding a new scheduling policy or a new car state must not require editing existing classes.

---

## 2. The key modelling insight

**A car's behaviour depends on its state**, and writing that as conditionals produces exactly the god class that loses this round:

```python
def step(self):
    if self.state == "MOVING_UP":
        if ...
    elif self.state == "IDLE":
        if ...
    elif self.state == "DOORS_OPEN":
        ...
```

Every new state (maintenance, emergency, overloaded) edits this method, and the transitions become impossible to follow.

**Use the State pattern:** each state is a class that knows what to do and which state comes next.

```
   ┌────────┐  request   ┌───────────┐  arrive   ┌────────────┐
   │  IDLE  │──────────► │  MOVING   │─────────► │ DOORS_OPEN │
   └────────┘            └───────────┘           └─────┬──────┘
        ▲                      ▲                       │ timeout
        │                      └───────────────────────┘
        │   no pending requests                  more requests
        └────────────────────────────────────────────────┘

   any state ──► MAINTENANCE ──► IDLE
```

---

## 3. Class diagram

```
   ┌──────────────────────┐
   │  ElevatorSystem      │  (facade / aggregate root)
   └──────────┬───────────┘
        ◆     │      ────────────────────┐
              ▼                          ▼
      ┌───────────────┐        ┌────────────────────────┐
      │ ElevatorCar   │        │ <<interface>>          │
      └───────┬───────┘        │ SchedulingStrategy     │
              │                └────────────────────────┘
              │ has-a                     △
              ▼                 ┌─────────┴──────────┐
   ┌────────────────────┐   NearestCar        LeastBusy
   │ <<interface>>      │
   │  ElevatorState     │        ┌──────────────────┐
   └────────────────────┘        │ <<interface>>    │
              △                  │ RequestObserver  │ (displays, logging)
   ┌──────────┼───────────┐      └──────────────────┘
 IdleState  MovingState  DoorsOpenState  MaintenanceState

   ┌──────────────┐   ┌─────────────────┐
   │ Request      │   │ Direction (enum)│
   │ (floor, dir) │   └─────────────────┘
   └──────────────┘
```

---

## 4. Implementation

```python
from abc import ABC, abstractmethod
from enum import Enum
from dataclasses import dataclass
from typing import Optional, List, Set
import heapq


class Direction(Enum):
    UP = 1
    DOWN = -1
    IDLE = 0


@dataclass(frozen=True)
class Request:
    floor: int
    direction: Direction        # IDLE for an internal (destination) request


# ---------- State pattern ----------

class ElevatorState(ABC):
    @abstractmethod
    def name(self) -> str: ...

    @abstractmethod
    def step(self, car: "ElevatorCar") -> None:
        # Advance one tick. Each state decides the next transition.
        ...

    def can_accept(self, car: "ElevatorCar") -> bool:
        return True


class IdleState(ElevatorState):
    def name(self): return "IDLE"

    def step(self, car):
        target = car.next_target()
        if target is None:
            return                              # stay idle
        if target == car.floor:
            car.set_state(DoorsOpenState())
        else:
            car.direction = Direction.UP if target > car.floor else Direction.DOWN
            car.set_state(MovingState())


class MovingState(ElevatorState):
    def name(self): return "MOVING"

    def step(self, car):
        car.floor += car.direction.value
        if car.should_stop_at(car.floor):
            car.set_state(DoorsOpenState())
        elif car.next_target() is None:
            car.direction = Direction.IDLE
            car.set_state(IdleState())


class DoorsOpenState(ElevatorState):
    def name(self): return "DOORS_OPEN"

    def step(self, car):
        car.serve_current_floor()               # clear requests at this floor
        if car.next_target() is None:
            car.direction = Direction.IDLE
            car.set_state(IdleState())
        else:
            car.set_state(MovingState())


class MaintenanceState(ElevatorState):
    def name(self): return "MAINTENANCE"
    def step(self, car): pass                   # deliberately does nothing
    def can_accept(self, car): return False     # refuses all new requests


# ---------- the car ----------

class ElevatorCar:
    def __init__(self, car_id: int, min_floor: int, max_floor: int, capacity: int = 10):
        self.id = car_id
        self.floor = min_floor
        self.min_floor, self.max_floor = min_floor, max_floor
        self.capacity = capacity
        self.occupancy = 0
        self.direction = Direction.IDLE
        self._state: ElevatorState = IdleState()
        self._up_stops: Set[int] = set()        # floors wanted while heading up
        self._down_stops: Set[int] = set()
        self._observers: List["RequestObserver"] = []

    # --- state ---
    @property
    def state_name(self) -> str:
        return self._state.name()

    def set_state(self, state: ElevatorState) -> None:
        self._state = state
        self._notify()

    def step(self) -> None:
        self._state.step(self)

    def can_accept(self) -> bool:
        return self._state.can_accept(self) and self.occupancy < self.capacity

    # --- requests ---
    def add_request(self, req: Request) -> None:
        if not (self.min_floor <= req.floor <= self.max_floor):
            raise ValueError("floor out of range")
        if req.direction is Direction.DOWN:
            self._down_stops.add(req.floor)
        else:
            self._up_stops.add(req.floor)
        self._notify()

    def should_stop_at(self, floor: int) -> bool:
        stops = self._up_stops if self.direction is Direction.UP else self._down_stops
        return floor in stops or floor in (self._up_stops | self._down_stops) and \
               self.next_target() == floor

    def serve_current_floor(self) -> None:
        self._up_stops.discard(self.floor)
        self._down_stops.discard(self.floor)
        self._notify()

    def next_target(self) -> Optional[int]:
        # SCAN: keep going in the current direction while stops remain ahead,
        # then reverse. This is the "elevator algorithm" and it is what real
        # elevators do -- it bounds waiting time, unlike nearest-request-first.
        ahead_up = sorted(f for f in self._up_stops if f > self.floor)
        ahead_down = sorted((f for f in self._down_stops if f < self.floor), reverse=True)

        if self.direction is Direction.UP:
            if ahead_up:   return ahead_up[0]
            if self._down_stops: return max(self._down_stops)
        elif self.direction is Direction.DOWN:
            if ahead_down: return ahead_down[0]
            if self._up_stops:   return min(self._up_stops)

        all_stops = self._up_stops | self._down_stops
        if not all_stops:
            return None
        return min(all_stops, key=lambda f: abs(f - self.floor))

    def pending(self) -> int:
        return len(self._up_stops) + len(self._down_stops)

    # --- observers ---
    def subscribe(self, obs: "RequestObserver") -> None:
        self._observers.append(obs)

    def _notify(self) -> None:
        for o in self._observers:
            o.on_change(self)


# ---------- observers ----------

class RequestObserver(ABC):
    @abstractmethod
    def on_change(self, car: ElevatorCar) -> None: ...


class FloorDisplay(RequestObserver):
    def on_change(self, car):
        print(f"[display] car {car.id}: floor {car.floor} "
              f"{car.direction.name} {car.state_name}")


# ---------- scheduling (Strategy) ----------

class SchedulingStrategy(ABC):
    @abstractmethod
    def choose(self, cars: List[ElevatorCar], req: Request) -> Optional[ElevatorCar]: ...


class NearestCarStrategy(SchedulingStrategy):
    def choose(self, cars, req):
        candidates = [c for c in cars if c.can_accept()]
        if not candidates:
            return None
        def cost(c: ElevatorCar) -> int:
            distance = abs(c.floor - req.floor)
            # Prefer a car already moving towards the request in the same direction.
            if c.direction is req.direction or c.direction is Direction.IDLE:
                return distance
            return distance + 2 * (c.max_floor - c.min_floor)   # penalty
        return min(candidates, key=cost)


class LeastBusyStrategy(SchedulingStrategy):
    def choose(self, cars, req):
        candidates = [c for c in cars if c.can_accept()]
        return min(candidates, key=lambda c: c.pending()) if candidates else None


# ---------- the system ----------

class ElevatorSystem:
    def __init__(self, cars: List[ElevatorCar], strategy: SchedulingStrategy):
        self._cars = cars
        self._strategy = strategy

    def request_external(self, floor: int, direction: Direction) -> Optional[int]:
        req = Request(floor, direction)
        car = self._strategy.choose(self._cars, req)
        if car is None:
            return None                       # all cars unavailable
        car.add_request(req)
        return car.id

    def request_internal(self, car_id: int, destination: int) -> None:
        car = next(c for c in self._cars if c.id == car_id)
        direction = Direction.UP if destination > car.floor else Direction.DOWN
        car.add_request(Request(destination, direction))

    def tick(self) -> None:
        for car in self._cars:
            car.step()
```

### Demonstration

```python
cars = [ElevatorCar(1, 0, 10), ElevatorCar(2, 0, 10)]
for c in cars:
    c.subscribe(FloorDisplay())

system = ElevatorSystem(cars, NearestCarStrategy())
assigned = system.request_external(5, Direction.UP)
system.request_internal(assigned, 8)

for _ in range(12):
    system.tick()
```

---

## 5. Design decisions

| Decision | Reason |
|---|---|
| **State pattern** for car behaviour | Each state owns its transitions; adding MAINTENANCE or EMERGENCY is a new class, and the `step` method never grows an `if` chain |
| Separate `_up_stops` / `_down_stops` | This is what makes SCAN expressible: a request at floor 5 going *down* is different from one at floor 5 going *up* |
| **SCAN**, not nearest-request-first | Nearest-first starves a far-away request indefinitely; SCAN bounds waiting time. This is the algorithmic content of the problem, and worth saying |
| `SchedulingStrategy` extracted | Assignment policy is the thing most likely to change (nearest, least busy, energy-optimal, zoned) |
| Observer for displays | The car does not know about displays, so adding a mobile app is another subscriber |
| `can_accept()` delegates to the state | Maintenance mode refuses requests without any caller knowing about maintenance |
| `ElevatorSystem` as a facade | Callers press buttons; they never touch car internals |

---

## 6. Follow-up questions

**"Why SCAN rather than always serving the nearest request?"**
Nearest-first can starve: if requests keep arriving near the car, a request at the far end is never served. SCAN sweeps to one extreme and reverses, giving a bound on waiting time. It is the same trade-off as SSTF versus SCAN in disk scheduling (`02_Core_CS/01_Operating_Systems`) — and pointing out that connection is a strong move.

**"How do you handle an emergency stop or fire mode?"**
A new `EmergencyState` whose `step` drives the car to the nearest floor, opens the doors and refuses everything. Because states are classes, this adds no conditionals anywhere. The transition is triggered by the system calling `car.set_state(EmergencyState())` on all cars.

**"What if a car is full?"**
`can_accept()` already checks occupancy, so the scheduler skips it and picks another. The subtlety worth raising is that occupancy is only known at the doors, so in reality the assignment may need revising after boarding — which argues for reassigning external requests rather than binding them permanently.

**"How would you support express elevators serving only floors 20–40?"**
Give `ElevatorCar` a `serviceable_floors` set and have the scheduler filter on it. No state or pricing change — it is a constraint on candidate selection.

**"How would you make this distributed across a building's controllers?"**
The car becomes a state machine replicated per controller, with assignment decided by a leader (or by a deterministic hash of the request so all controllers agree without coordination). The interesting problem becomes consistency of the pending-request set.

**"What is the concurrency story?"**
Button presses arrive from many sources. In this model, `add_request` mutates sets — so either guard it with a lock per car, or (better) have each car own a single-threaded event loop consuming a request queue, which removes the shared-state problem entirely.

---

## 7. Practice

- [ ] Redesign from scratch in 45 minutes
- [ ] Add an `EmergencyState` and a system-wide `trigger_emergency()`
- [ ] Add floor restrictions per car and adapt the scheduler
- [ ] Replace the tick loop with an event queue per car
- [ ] Write a simulation that measures average wait time under SCAN vs nearest-first

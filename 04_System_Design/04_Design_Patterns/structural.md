# Structural Patterns

> Patterns about **how objects are composed** — assembling classes and objects into larger structures while keeping them flexible.

---

## Adapter

**Intent.** Convert one interface into another that clients expect. Also called a Wrapper.

**When it applies.** You have working code and a third-party or legacy component whose interface does not match — and you cannot change either.

```python
class PaymentProcessor(ABC):                 # what our code expects
    @abstractmethod
    def pay(self, amount_paise: int) -> bool: ...

class ThirdPartyGateway:                     # what the vendor gives us
    def make_payment(self, rupees: float, currency: str) -> dict: ...

class GatewayAdapter(PaymentProcessor):      # the adapter
    def __init__(self, gateway: ThirdPartyGateway):
        self._g = gateway

    def pay(self, amount_paise: int) -> bool:
        result = self._g.make_payment(amount_paise / 100.0, "INR")
        return result.get("status") == "OK"
```

**The value in a system design context:** every external dependency should sit behind an adapter you own. Then swapping a payment provider, an email provider or a storage backend is a new adapter class, and nothing in your domain changes. It is Dependency Inversion applied at the system boundary.

**Real uses:** `Arrays.asList`, `InputStreamReader` (bytes → characters), ORM drivers, and essentially every SDK wrapper in a well-structured codebase.

---

## Decorator

**Intent.** Attach responsibilities to an object **dynamically**, without subclassing.

**The problem it solves — the class explosion.** With coffee, milk, sugar, cream and syrup as options, subclassing gives `CoffeeWithMilkAndSugar`, `CoffeeWithMilkAndCream`… 2ⁿ classes for n options.

```python
class Coffee(ABC):
    @abstractmethod
    def cost(self) -> int: ...
    @abstractmethod
    def description(self) -> str: ...

class SimpleCoffee(Coffee):
    def cost(self): return 5000
    def description(self): return "coffee"

class CoffeeDecorator(Coffee):               # wraps another Coffee
    def __init__(self, inner: Coffee):
        self._inner = inner

class Milk(CoffeeDecorator):
    def cost(self): return self._inner.cost() + 1000
    def description(self): return self._inner.description() + " + milk"

class Sugar(CoffeeDecorator):
    def cost(self): return self._inner.cost() + 500
    def description(self): return self._inner.description() + " + sugar"

drink = Sugar(Milk(SimpleCoffee()))
```

**The key property:** decorators **compose in any order and any number**, because each one *is* the interface it wraps. n options give n classes, not 2ⁿ.

**Where you have already met it:** Java's I/O streams (`new BufferedReader(new InputStreamReader(new FileInputStream(f)))`) — the canonical example — and Python's function decorators, which are the same idea at the language level.

**In system design:** middleware chains. Logging, authentication, rate limiting, compression and tracing each wrap the handler, and the order is configurable. `WeekendPricing(HourlyPricing())` in the parking-lot case study is exactly this.

**Adapter vs Decorator** — a common exam question: **Adapter changes the interface** and keeps the behaviour; **Decorator keeps the interface** and adds behaviour.

---

## Facade

**Intent.** Provide a simplified interface to a complex subsystem.

```python
class VideoConverter:                        # the facade
    def convert(self, path: str, target_format: str) -> str:
        file = VideoFile(path)
        codec = CodecFactory.extract(file)
        buffer = BitrateReader.read(path, codec)
        result = BitrateReader.convert(buffer, target_format)
        return AudioMixer().fix(result)
```

The caller writes one line instead of five, and does not depend on five classes.

**The important property:** a facade **does not hide** the subsystem — advanced callers can still use the parts directly. It offers a convenient default path, not a wall.

**In system design:** an **API gateway** is a facade over a microservice architecture. A client makes one call; the gateway fans out to five services and aggregates. The Backend-for-Frontend pattern is a facade per client type.

**Facade vs Adapter:** Facade simplifies **many** interfaces into one convenient one; Adapter converts **one** interface into another specific one.

---

## Proxy

**Intent.** Provide a placeholder that controls access to another object.

Four varieties, distinguished by what the control is *for*:

| Type | Purpose | Example |
|---|---|---|
| **Virtual** | delay expensive creation until needed | lazy-loading a large image or an ORM association |
| **Protection** | enforce access control | permission checks before delegating |
| **Remote** | represent an object in another address space | RPC and gRPC client stubs |
| **Caching** | return a stored result instead of recomputing | memoisation, an HTTP caching proxy |

```python
class ImageProxy(Image):
    def __init__(self, filename: str):
        self._filename, self._real = filename, None

    def display(self):
        if self._real is None:               # load only on first use
            self._real = RealImage(self._filename)
        self._real.display()
```

**Proxy vs Decorator** — they look identical structurally, and the distinction is *intent*: a **decorator adds behaviour** the caller wants; a **proxy controls access** to something the caller may not even know is remote, lazy or cached.

**In system design:** reverse proxies, service-mesh sidecars, CDN edges and connection poolers are all proxies. The sidecar in a service mesh is a proxy that adds retries, circuit breaking and mTLS without the application knowing.

---

## Composite

**Intent.** Compose objects into tree structures and let clients treat individual objects and compositions **uniformly**.

```python
class FileSystemNode(ABC):
    @abstractmethod
    def size(self) -> int: ...

class File(FileSystemNode):                  # leaf
    def __init__(self, bytes_: int): self._bytes = bytes_
    def size(self): return self._bytes

class Directory(FileSystemNode):             # composite
    def __init__(self): self._children = []
    def add(self, node: FileSystemNode): self._children.append(node)
    def size(self): return sum(c.size() for c in self._children)
```

**The property that matters:** the client calls `size()` without knowing whether it holds a file or a directory containing ten thousand files. Recursion is hidden inside the structure.

**Where you meet it:** file systems, UI widget trees (a panel contains buttons and other panels), organisational hierarchies, and abstract syntax trees.

**The tension it creates:** if the composite interface includes `add()` and `remove()`, leaves must implement them meaninglessly — an Interface Segregation problem. The alternative is to declare them only on `Composite`, which means clients must sometimes know the difference, weakening the uniformity that was the point. There is no clean resolution; pick the trade-off deliberately.

---

## Bridge

**Intent.** Separate an abstraction from its implementation so the two can vary independently.

**The problem — a class explosion along two axes.** Shapes (circle, square, triangle) × renderers (vector, raster, SVG) gives 9 classes by inheritance, and 12 when a fourth shape appears.

```python
class Renderer(ABC):                         # the implementation axis
    @abstractmethod
    def render_circle(self, r: float): ...

class VectorRenderer(Renderer): ...
class RasterRenderer(Renderer): ...

class Shape(ABC):                            # the abstraction axis
    def __init__(self, renderer: Renderer):
        self._renderer = renderer            # composition, not inheritance

class Circle(Shape):
    def __init__(self, renderer, radius):
        super().__init__(renderer); self._radius = radius
    def draw(self): self._renderer.render_circle(self._radius)
```

**Result:** m + n classes instead of m × n, and each axis extends independently.

**Bridge vs Adapter:** Bridge is designed in **up front** to keep two dimensions independent; Adapter is applied **after the fact** to reconcile interfaces you did not control.

**In system design:** a storage abstraction with S3, GCS and local-disk implementations; a messaging abstraction over Kafka, SQS and an in-memory queue for tests. The domain code depends on the abstraction and never on the vendor.

---

## Flyweight

**Intent.** Share common state between many objects to reduce memory.

Split state into **intrinsic** (shared, immutable — a character's glyph shape, a tree species' texture) and **extrinsic** (per-instance — position, colour), and store only the extrinsic state per object.

```python
class TreeType:                              # intrinsic, shared
    _cache = {}
    def __new__(cls, name, texture):
        key = (name, texture)
        if key not in cls._cache:
            cls._cache[key] = super().__new__(cls)
        return cls._cache[key]

class Tree:                                  # extrinsic, per instance
    def __init__(self, x, y, tree_type: TreeType):
        self.x, self.y, self.type = x, y, tree_type   # type is shared
```

A forest of a million trees stores a million `(x, y, pointer)` triples and a handful of `TreeType` objects, rather than a million copies of a texture.

**Where you meet it:** Java's `Integer` cache for −128…127 (which is exactly why `==` on boxed integers is a trap), string interning, glyph caches in text rendering, and particle systems.

**Only worth it when you have a very large number of objects with substantial shared state.** Otherwise it adds indirection for nothing.

---

## Choosing between them

| Situation | Pattern |
|---|---|
| Two incompatible interfaces must work together | **Adapter** |
| Add behaviour dynamically, composably | **Decorator** |
| Simplify a complex subsystem for callers | **Facade** |
| Control access — lazy, remote, cached, permissioned | **Proxy** |
| Treat individual objects and trees uniformly | **Composite** |
| Two dimensions varying independently | **Bridge** |
| Very many objects with shared immutable state | **Flyweight** |

---

## Recall questions

1. State the difference between Adapter and Decorator in one sentence.
2. State the difference between Facade and Adapter.
3. State the difference between Proxy and Decorator, given that they are structurally identical.
4. Why does Decorator give n classes where subclassing gives 2ⁿ?
5. Give the canonical Decorator example from the Java standard library.
6. Name the four kinds of Proxy with an example each.
7. What system-design components are proxies?
8. What tension does Composite create with Interface Segregation?
9. What class explosion does Bridge solve, and what is the resulting count?
10. Distinguish intrinsic from extrinsic state in Flyweight, and give the Java example.
11. Why is "every external dependency behind an adapter you own" good system design?

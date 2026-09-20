
# Geometry and Mensuration

> **Why it matters.** 1-3 questions per paper. It is almost entirely a **formula-recall** topic:
> if you know the formula the question takes 30 seconds, and if you do not it takes forever. Treat
> §2 as a memorisation target, not as reading.

---

## 1. Plane geometry essentials

### Triangles
```
Angle sum = 180°.  Exterior angle = sum of the two remote interior angles.
Sum of any two sides > the third side           (triangle inequality)
The largest angle is opposite the largest side
```

| Property | Statement |
|---|---|
| Pythagoras | `a² + b² = c²` for a right triangle |
| Common triples ⭐ | (3,4,5) (5,12,13) (7,24,25) (8,15,17) (9,40,41) (20,21,29) and all their multiples |
| Median | Joins a vertex to the opposite midpoint; the three meet at the **centroid**, which divides each in a **2:1** ratio ⭐ |
| Altitude | Perpendicular from a vertex; meet at the **orthocentre** |
| Angle bisector | Meets at the **incentre** (centre of the inscribed circle) |
| Perpendicular bisector | Meets at the **circumcentre** (centre of the circumscribed circle) |
| Similar triangles | Equal angles ⇒ sides proportional; **area ratio = (side ratio)²** ⭐⭐ |
| Congruence criteria | SSS, SAS, ASA, AAS, RHS |
| Angle bisector theorem | The bisector from A divides BC in the ratio `AB : AC` |
| Midpoint theorem | The segment joining two midpoints is parallel to the third side and half its length |

**Area of a triangle — pick the right one:**
```
½ × base × height
½ ab sin C
Heron: √[s(s−a)(s−b)(s−c)]  where s = (a+b+c)/2          ⭐ when all three sides are known
Equilateral: (√3/4) a²                 Height of equilateral: (√3/2) a
```

### Circles
```
Circumference = 2πr          Area = πr²
Arc length    = (θ/360) × 2πr           Sector area = (θ/360) × πr²
Segment area  = sector area − triangle area
```

| Theorem | Statement |
|---|---|
| Angle at the centre | Twice the angle at the circumference on the same arc ⭐ |
| Angle in a semicircle | 90° |
| Angles in the same segment | Equal |
| Cyclic quadrilateral | Opposite angles sum to 180° ⭐ |
| Tangent | Perpendicular to the radius at the point of contact |
| Two tangents from an external point | Equal in length |
| Tangent-secant (power of a point) | `PT² = PA × PB` |
| Intersecting chords | `PA × PB = PC × PD` |

### Quadrilaterals and polygons
```
Sum of interior angles of an n-gon = (n − 2) × 180°
Sum of exterior angles              = 360°   (always, for any n) ⭐
Each interior angle of a regular n-gon = (n − 2) × 180°/n
Number of diagonals = n(n − 3)/2
```

---

## 2. The mensuration formula table ⭐⭐⭐

### 2-D

| Shape | Area | Perimeter |
|---|---|---|
| Square (side a) | `a²` | `4a`; diagonal `a√2` |
| Rectangle (l, b) | `lb` | `2(l + b)`; diagonal `√(l² + b²)` |
| Triangle | `½bh` | `a + b + c` |
| Equilateral (a) | `(√3/4)a²` | `3a` |
| Parallelogram | `base × height` | `2(a + b)` |
| Rhombus (diagonals d₁, d₂) | `½ d₁d₂` ⭐ | `4a`, where `a = ½√(d₁² + d₂²)` |
| Trapezium | `½(a + b)h` ⭐ | sum of the sides |
| Circle (r) | `πr²` | `2πr` |
| Semicircle | `½πr²` | `πr + 2r` ⚠️ (do not forget the diameter) |
| Ring / annulus (R, r) | `π(R² − r²)` | — |
| Regular hexagon (a) | `(3√3/2)a²` | `6a` |

### 3-D

| Solid | Volume | Curved / Lateral SA | Total SA |
|---|---|---|---|
| Cube (a) | `a³` | `4a²` | `6a²`; diagonal `a√3` |
| Cuboid (l,b,h) | `lbh` | `2h(l + b)` | `2(lb + bh + hl)`; diagonal `√(l²+b²+h²)` ⭐ |
| Cylinder (r,h) | `πr²h` | `2πrh` | `2πr(r + h)` |
| Cone (r,h,l) | `⅓πr²h` | `πrl` | `πr(r + l)`; slant `l = √(r² + h²)` ⭐ |
| Sphere (r) | `(4/3)πr³` | — | `4πr²` |
| Hemisphere (r) | `(2/3)πr³` | `2πr²` | `3πr²` ⚠️ (curved + flat circle) |
| Frustum (R,r,h) | `⅓πh(R² + r² + Rr)` | `πl(R + r)`, `l = √(h² + (R−r)²)` | + the two circles |
| Prism | `base area × height` | `perimeter × height` | + 2 × base area |
| Pyramid | `⅓ × base area × height` | `½ × perimeter × slant height` | + base area |

---

## 3. Shortcuts and traps ⭐

```
⭐⭐ SCALING LAW. If every linear dimension is multiplied by k:
      lengths ×k        areas ×k²        volumes ×k³
   This single rule answers a large fraction of mensuration questions instantly.
   "If the radius of a sphere doubles, the volume becomes 8 times."
⭐ Similar figures: area ratio = (linear ratio)²; volume ratio = (linear ratio)³.
⚠️ Total surface area vs curved surface area. Read which is asked — the difference is the
   flat faces, and both values are always among the options.
⚠️ A hemisphere's TOTAL surface is 3πr², not 2πr²: the curved part plus the flat circular face.
⚠️ Semicircle perimeter is πr + 2r, not πr.
⭐ For a cone, always compute the slant height l = √(r² + h²) before using πrl.
⭐ Use π = 22/7 when the radius is a multiple of 7, and 3.14 otherwise. Check which the options
   assume.
⚠️ A "cube of side a cut into cubes of side a/n" gives n³ small cubes, and the total surface area
   becomes n times the original.
⭐ Painted-cube questions: for an n×n×n cube painted outside and cut into unit cubes,
     3 faces painted (corners)      = 8
     2 faces painted (edges)        = 12(n − 2)
     1 face painted  (face centres) = 6(n − 2)²
     0 faces painted (interior)     = (n − 2)³
   Check: 8 + 12(n−2) + 6(n−2)² + (n−2)³ = n³ ✓
```

---

## 4. Fully solved examples

### Example 1 — The scaling law in disguise ⭐⭐
**The radius of a cylinder is increased by 20% and its height is decreased by 10%. Find the
percentage change in its volume.**

```
V = πr²h, so V ∝ r²h.
New V / Old V = (1.20)² × (0.90) = 1.44 × 0.90 = 1.296
Change = **+29.6%**
```
⚠️ The radius contributes **squared**. Answering "+20 − 10 = +10%" is the trap.

---

### Example 2 — Cone, sphere and melting ⭐
**A solid metallic sphere of radius 6 cm is melted and recast into a cone of base radius 4 cm. Find
the height of the cone.**

Volume is conserved:
```
(4/3)π(6)³ = (1/3)π(4)²h
(4/3)(216)  = (1/3)(16)h
288         = 16h/3
864         = 16h
h = **54 cm**
```
⭐ **Every "melted and recast" question is just "set the two volumes equal".** The `π` and the
fractions cancel; do that cancellation *before* multiplying out.

---

### Example 3 — The painted cube ⭐⭐
**A cube of side 5 cm is painted on all faces and then cut into 1 cm cubes. How many small cubes
have exactly 2 faces painted, and how many have none?**

```
n = 5
Exactly 2 faces (edges, excluding corners) = 12(n − 2) = 12 × 3 = **36**
No faces painted (interior)                = (n − 2)³   = 3³   = **27**

Full check: 8 + 36 + 6(9) + 27 = 8 + 36 + 54 + 27 = 125 = 5³ ✓
```

---

### Example 4 — Similar triangles with an area ratio
**In triangle ABC, DE is parallel to BC with D on AB and E on AC, such that `AD : DB = 2 : 3`. If
the area of triangle ADE is 8 cm², find the area of trapezium DBCE.**

```
AD : AB = 2 : 5  (since AD:DB = 2:3)
Triangles ADE and ABC are similar (DE ∥ BC).
Area ratio = (linear ratio)² = (2/5)² = 4/25

Area(ABC) = 8 × 25/4 = 50 cm²
Area(DBCE) = 50 − 8 = **42 cm²**
```

---

### Example 5 — Combined solid
**A toy is in the shape of a cone mounted on a hemisphere of the same radius 7 cm. The total height
of the toy is 31 cm. Find its total surface area. (Take π = 22/7.)**

```
Radius r = 7 cm
Hemisphere height = r = 7 cm
Cone height h = 31 − 7 = 24 cm
Slant height l = √(7² + 24²) = √(49 + 576) = √625 = 25 cm    ⭐ a 7-24-25 triple

Total SA = curved SA of cone + curved SA of hemisphere
         = πrl + 2πr²
         = π(7)(25) + 2π(49)
         = π(175 + 98)
         = (22/7)(273)
         = 22 × 39
         = **858 cm²**
```
⚠️ Note that the flat circular face of the hemisphere is *not* included — it is glued to the cone's
base. Reading the geometry before applying formulas is the whole skill here.

---

### Example 6 — Circle theorem
**Two chords AB and CD of a circle intersect at P inside the circle. If `AP = 6`, `PB = 4` and
`CP = 3`, find `PD`.**

```
Intersecting chords theorem: AP × PB = CP × PD
6 × 4 = 3 × PD
24 = 3 PD
PD = **8**
```

---

### Example 7 — Area of a path ⚠️
**A rectangular garden is 40 m by 30 m. A path 2 m wide runs all around it on the outside. Find the
area of the path.**

```
Outer dimensions = (40 + 2×2) × (30 + 2×2) = 44 × 34 = 1496 m²
Garden area      = 40 × 30 = 1200 m²
Path area        = 1496 − 1200 = **296 m²**
```
⚠️ The path adds the width on **both** sides, so each dimension grows by `2w`, not `w`. If the path
were *inside*, each dimension would shrink by `2w`: `36 × 26 = 936`, path `= 1200 − 936 = 264 m²` —
a different answer, and both appear among the options.

---

## 5. Practice set

1. Area of an equilateral triangle of side 12 cm.
2. Diagonal of a cuboid 6 × 8 × 10.
3. A cone has radius 5 and height 12. Find its slant height and curved surface area.
4. If a sphere's radius increases by 50%, by what % does its surface area increase?
5. Area of a rhombus with diagonals 16 cm and 30 cm; also its side.
6. A cylinder and a cone have the same radius and height. Ratio of their volumes?
7. Number of diagonals of a regular decagon.
8. A 4×4×4 cube painted and cut into unit cubes: how many have exactly one face painted?
9. Perimeter of a semicircle of radius 14 cm (π = 22/7).
10. Area of a trapezium with parallel sides 12 and 20 and height 9.

<details><summary>Answers</summary>

1. `(√3/4)(144) = 36√3 ≈ **62.35 cm²**`.
2. `√(36+64+100) = √200 = **10√2 ≈ 14.14**`.
3. `l = 13`; `CSA = π(5)(13) = **65π ≈ 204.2**`.
4. `(1.5)² = 2.25` → **125% increase**.
5. Area `= ½(16)(30) = **240 cm²**`; side `= ½√(256+900) = ½(34) = **17 cm**`.
6. `πr²h : ⅓πr²h = **3 : 1**`.
7. `10(10−3)/2 = **35**`.
8. `6(n−2)² = 6(4) = **24**`.
9. `πr + 2r = 44 + 28 = **72 cm**`.
10. `½(12+20)(9) = **144**`.
</details>

---

## Recall questions

1. State the scaling law for lengths, areas and volumes.
2. Give the total surface area of a hemisphere and explain the extra term.
3. Give the cone's slant-height relation and the CSA formula.
4. List the painted-cube counts for an `n×n×n` cube and verify they sum to `n³`.
5. State the intersecting-chords and tangent-secant theorems.
6. What is the area ratio of similar triangles?
7. How does a path *outside* a rectangle change the dimensions, and how does one *inside* differ?

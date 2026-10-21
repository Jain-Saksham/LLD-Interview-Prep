# UML Class Diagrams

## Quick Summary
- **Definition:** A static diagram showing classes, their attributes/methods, and relationships between them.
- **Problem it solves:** Communicating a design quickly on a whiteboard before writing code, which is the first step in most LLD interviews.
- **Use when:** Laying out entities and relationships for a design problem.
- **Avoid:** Over-detailing. Interviewers want key classes, relationships, and multiplicities, not every getter.

## Key Takeaways

**Class box:**
```text
+---------------------------+
|        ParkingLot         |   ← name (italic or «abstract» for abstract classes)
+---------------------------+
| - name: String            |   ← attributes: visibility name: Type
| - floors: List<Floor>     |
+---------------------------+
| + park(v: Vehicle): Ticket|   ← methods: visibility name(params): ReturnType
| - findSpot(): Spot        |
+---------------------------+
```
**Visibility:** `+` public, `-` private, `#` protected, `~` package-private. Static members are underlined.

**Relationships (weakest → strongest coupling):**
| Relationship | Notation | Meaning | Example |
|---|---|---|---|
| Dependency | `A - - -> B` (dashed arrow) | A temporarily uses B (param/local) | `Printer - - -> Document` |
| Association | `A ——— B` or `A ——> B` | A holds a reference to B | `Doctor ——— Patient` |
| Aggregation | `A ◇——— B` | A has B; B can live independently | `Team ◇——— Player` |
| Composition | `A ◆——— B` | A owns B; B dies with A | `House ◆——— Room` |
| Inheritance | `B ——▷ A` (solid, hollow triangle) | B extends A | `Car ——▷ Vehicle` |
| Realization | `B - - -▷ A` (dashed, hollow triangle) | B implements interface A | `Card - - -▷ Payment` |

**Multiplicity:** `1`, `0..1`, `*` / `0..*`, `1..*`, e.g., `ParkingLot 1 ◆—— 1..* Floor`.

- Diamonds sit on the **whole**; triangles point to the **parent/interface**.
- Mark interfaces with `«interface»` and abstract classes with `«abstract»` or italics.
- In interviews: start with **nouns → classes**, **verbs → methods**, then add relationships and multiplicities.

## Java Example
How each relationship appears in code:

```java
import java.util.ArrayList;
import java.util.List;

interface Payment { void pay(double amt); }                 // «interface»

class CardPayment implements Payment {                      // Realization: CardPayment - - -▷ Payment
    public void pay(double amt) { System.out.println("Paid " + amt); }
}

abstract class Vehicle { abstract int wheels(); }           // «abstract»
class Car extends Vehicle { int wheels() { return 4; } }    // Inheritance: Car ——▷ Vehicle

class Spot {
    private Vehicle parked;                                 // Association: Spot ——> Vehicle (0..1)
    void park(Vehicle v) { parked = v; }
}

class Floor {
    private final List<Spot> spots = new ArrayList<>();     // Composition: Floor ◆—— 1..* Spot
    Floor(int n) { for (int i = 0; i < n; i++) spots.add(new Spot()); }
    Spot firstSpot() { return spots.get(0); }
}

class ParkingLot {
    void checkout(Payment p, double fee) { p.pay(fee); }    // Dependency: ParkingLot - - -> Payment
}

class Main {
    public static void main(String[] args) {
        Floor floor = new Floor(10);
        floor.firstSpot().park(new Car());
        new ParkingLot().checkout(new CardPayment(), 50);
    }
}
```

## Interview Questions
**Q1. Dependency vs association?** *(most common)*
Dependency is temporary use (method parameter or local variable), shown with a dashed arrow. Association is a structural, longer-lived reference (a field), shown with a solid line.

**Q2. How do you show inheritance vs interface implementation?**
Inheritance: solid line with a hollow triangle pointing to the parent. Realization: dashed line with a hollow triangle pointing to the interface.

**Q3. What does multiplicity `1..*` mean?**
One or more. E.g., a `Floor` has `1..*` `Spot`s.

**Q4. How much detail should a UML diagram have in an interview?**
Core classes, key attributes and methods, relationships with multiplicities. Skip getters/setters and trivial fields.

**Q5. What's the difference between class and sequence diagrams?**
Class diagrams show static structure. Sequence diagrams show interactions over time (method call order between objects).

## Common Pitfalls
- Placing the diamond on the part instead of the whole.
- Drawing every class as inheritance when composition fits better.
- Spending too long on notation perfection instead of design decisions.

## Related Topics
- [Association, Aggregation, Composition](Association-Aggregation-Composition.md)
- [Class vs Interface vs Abstract Class](Class-vs-Interface-vs-Abstract-Class.md)
- [LLD Case Studies](../LLD-Case-Studies/README.md)

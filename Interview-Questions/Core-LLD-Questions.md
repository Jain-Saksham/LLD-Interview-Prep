# Core LLD Interview Questions

OOP, relationships, and how to approach an LLD round. Topic-specific Q&A lives under [Core Concepts](../Core-Concepts/README.md).

## OOP and Language
**Q1. Explain the four OOP pillars with one real example.**
A bank `Account`: private balance (encapsulation), `deposit/withdraw` API (abstraction), `SavingsAccount extends Account` (inheritance), `account.withdraw()` behaving differently per type (polymorphism). See [OOP Principles](../Core-Concepts/OOP-Principles.md).

**Q2. Overloading vs overriding?**
Overloading: same name, different parameters, resolved at compile time. Overriding: same signature in a subclass, resolved at runtime by object type.

**Q3. Why prefer immutable objects?**
Thread-safe without locks, safe to share and cache, simpler reasoning, and safe as `HashMap` keys. Achieve with `final` class/fields, no setters, and defensive copies of mutable fields.

**Q4. Why must you override `hashCode()` when overriding `equals()`?**
Equal objects must have equal hash codes, or hash-based collections (`HashMap`, `HashSet`) will fail to find them.

**Q5. Interface vs abstract class, in one sentence?**
Interface = capability contract with no state, and you can implement many. Abstract class = shared state and partial implementation, and you can extend one. See [Class vs Interface vs Abstract Class](../Core-Concepts/Class-vs-Interface-vs-Abstract-Class.md).

## Design Relationships
**Q6. Is-a vs has-a?**
Is-a = inheritance (`Car` is a `Vehicle`). Has-a = composition/aggregation (`Car` has an `Engine`). Prefer has-a unless is-a is genuinely substitutable. See [Composition vs Inheritance](../Core-Concepts/Composition-vs-Inheritance.md).

**Q7. Classify these: Order–OrderItem, Department–Professor, Driver–Car.**
Order ◆ OrderItem: composition (items don't exist without the order). Department ◇ Professor: aggregation (professors exist independently). Driver — Car: association (uses). See [Association, Aggregation, Composition](../Core-Concepts/Association-Aggregation-Composition.md).

**Q8. What makes a design "good"?**
High cohesion, low coupling, clear responsibilities, extension points where change is likely, testability, and no unnecessary complexity. See [Coupling and Cohesion](../Core-Concepts/Coupling-and-Cohesion.md).

## Approaching an LLD Round
**Q9. What steps do you follow in an LLD interview?**
1. **Clarify requirements** (functional + key constraints; agree on scope).
2. **Identify entities** (nouns → classes) and **behaviors** (verbs → methods).
3. **Define relationships** and draw a quick [UML class diagram](../Core-Concepts/UML-Class-Diagrams.md).
4. **Define interfaces** for parts likely to vary (payment, pricing, notification).
5. **Apply patterns only where justified** and state the trade-off.
6. **Write core code**: key classes and the main flow, not every getter.
7. **Discuss extensions**: concurrency, new requirements, testing.

**Q10. How do you handle concurrency in LLD problems (e.g., booking the same seat)?**
Identify shared mutable state, then lock at the right granularity (`synchronized` on the seat/spot, `ReentrantLock`, or `ConcurrentHashMap.compute`), or use optimistic locking with a version check. Mention DB-level locking for persistence.

**Q11. How do you make a design extensible?**
Program to interfaces at points of variation, use Strategy/Factory for new types, keep classes focused ([SRP](../SOLID/Single-Responsibility-Principle.md)), and inject dependencies ([DI](../Core-Concepts/Dependency-Injection.md)).

**Q12. Enum vs class hierarchy for types (e.g., VehicleType)?**
Use an enum when types are fixed and carry only data or simple behavior. Use a class hierarchy or Strategy when each type has substantial distinct behavior or new types are added often.

**Q13. How do you avoid over-engineering in an interview?**
Start simple, meet the stated requirements, and introduce abstractions only at clear variation points. Say the trade-off out loud ("I'd add a Strategy here if more pricing rules are expected").

## Related Topics
- [Core Concepts Overview](../Core-Concepts/README.md)
- [LLD Case Studies](../LLD-Case-Studies/README.md)
- [SOLID Questions](SOLID-Questions.md)

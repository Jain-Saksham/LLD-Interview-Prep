# SOLID Interview Questions

Cross-principle and scenario questions. For principle-specific Q&A, see each page under [SOLID](../SOLID/README.md).

## Rapid Fire
**Q1. State all five SOLID principles in one line each.**
SRP: one reason to change. OCP: extend without modifying. LSP: subtypes must be substitutable. ISP: no client depends on methods it doesn't use. DIP: depend on abstractions, not concretions.

**Q2. Which SOLID principle is most important?**
There's no single answer. A strong response: OCP is the goal, and SRP/LSP/ISP/DIP are the means to achieve it. Or: SRP, because most design rot starts with classes doing too much.

**Q3. Are SOLID principles always worth applying?**
No. They add abstractions and classes. Apply them where change is likely (volatile requirements, external dependencies). Over-applying produces needless indirection (YAGNI).

## Spot the Violation
**Q4. A `ReportService` fetches data from the DB, calculates totals, and generates a PDF. What's wrong?**
[SRP](../SOLID/Single-Responsibility-Principle.md): three reasons to change (storage, business rules, format). Split into repository, calculator, and formatter.

**Q5. A `calculateShipping()` method has a `switch` on carrier type, and a new carrier is added every quarter.**
[OCP](../SOLID/Open-Closed-Principle.md): replace with a `ShippingStrategy` interface and one class per carrier ([Strategy](../Design-Patterns/Behavioral/Strategy.md)).

**Q6. `ReadOnlyFile extends File` and overrides `write()` to throw an exception.**
[LSP](../SOLID/Liskov-Substitution-Principle.md) (callers of `File.write()` break) and a hint of [ISP](../SOLID/Interface-Segregation-Principle.md). Split into `Readable` and `Writable` interfaces.

**Q7. A `Worker` interface has `work()` and `eat()`, and `RobotWorker` must implement `eat()`.**
[ISP](../SOLID/Interface-Segregation-Principle.md): split into `Workable` and `Eatable`.

**Q8. `NotificationService` does `new SmtpEmailClient()` in its constructor, and tests send real emails.**
[DIP](../SOLID/Dependency-Inversion-Principle.md): depend on an `EmailClient` interface and inject it ([DI](../Core-Concepts/Dependency-Injection.md)).

## Interplay and Trade-offs
**Q9. How are ISP and LSP related?**
Fat interfaces force implementers to stub or throw for unsupported methods, which breaks substitutability. Segregating interfaces often fixes LSP violations.

**Q10. How do OCP, LSP, and DIP work together?**
DIP makes high-level code depend on an abstraction. OCP adds new behavior as new implementations of it. LSP guarantees those implementations behave correctly when substituted.

**Q11. Can following SRP violate another principle or goal?**
Over-splitting can lower readability and scatter related logic (hurting cohesion at the feature level). Balance with "things that change together stay together".

**Q12. Which design patterns map to which SOLID principles?**
| Principle | Patterns |
|---|---|
| SRP | Facade, Command, Decorator (one feature per class) |
| OCP | Strategy, Decorator, Observer, Template Method, Visitor |
| LSP | Any polymorphic pattern relies on it (Composite, State, Strategy) |
| ISP | Adapter (exposes only what the client needs), role interfaces |
| DIP | Factory Method, Abstract Factory, Dependency Injection |

**Q13. How would you refactor legacy code toward SOLID without a rewrite?**
Add tests around the area first, then make incremental changes: extract classes (SRP), introduce interfaces at seams (DIP), replace conditionals with polymorphism where variants grow (OCP). Refactor only the parts that are actually changing.

## Related Topics
- [SOLID Overview](../SOLID/README.md)
- [Coupling and Cohesion](../Core-Concepts/Coupling-and-Cohesion.md)
- [Design Pattern Questions](Design-Pattern-Questions.md)

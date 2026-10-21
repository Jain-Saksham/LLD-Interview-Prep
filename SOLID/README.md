# SOLID Principles

Five object-oriented design principles (popularized by Robert C. Martin) that keep code easy to change. They are **guidelines, not laws** — apply them where change is likely, not everywhere by reflex.

| Principle | One-liner | Smell when violated |
|---|---|---|
| [**S**ingle Responsibility](Single-Responsibility-Principle.md) | A class should have one reason to change. | "God class", unrelated edits collide in one file |
| [**O**pen/Closed](Open-Closed-Principle.md) | Extend behavior without editing existing code. | Growing `if/else` or `switch` on type |
| [**L**iskov Substitution](Liskov-Substitution-Principle.md) | Subtypes must be usable wherever the base type is. | `instanceof` checks, overrides that throw |
| [**I**nterface Segregation](Interface-Segregation-Principle.md) | Don't force clients to depend on methods they don't use. | Empty or `UnsupportedOperationException` implementations |
| [**D**ependency Inversion](Dependency-Inversion-Principle.md) | Depend on abstractions, not concretions. | `new ConcreteThing()` inside business logic |

## How They Fit Together
- **SRP + ISP** keep units small and focused.
- **OCP** is the goal; **LSP** makes polymorphism safe enough to achieve it; **DIP** is the wiring that makes it practical.
- Many design patterns are SOLID applied: [Strategy](../Design-Patterns/Behavioral/Strategy.md) (OCP), [Decorator](../Design-Patterns/Structural/Decorator.md) (OCP + SRP), [Factory Method](../Design-Patterns/Creational/Factory-Method.md) (DIP).

## Related
- [SOLID Interview Questions](../Interview-Questions/SOLID-Questions.md)
- [Coupling and Cohesion](../Core-Concepts/Coupling-and-Cohesion.md)
- [Dependency Injection](../Core-Concepts/Dependency-Injection.md)

# LLD Interview Prep

A personal, revision-friendly **Low-Level Design handbook** for Java interviews. Every topic is a single Markdown page: a quick summary, key takeaways, a minimal Java example, interview Q&A, pitfalls, and links to related topics.

---

## Table of Contents

| Category | What's inside |
|---|---|
| [Core Concepts](Core-Concepts/README.md) | OOP, composition vs inheritance, relationships, UML, coupling/cohesion, DI |
| [SOLID Principles](SOLID/README.md) | The five design principles, each with a violation and a fix |
| [Design Patterns](Design-Patterns/README.md) | 23 GoF patterns: Creational, Structural, Behavioral |
| [Interview Questions](Interview-Questions/README.md) | Cross-topic rapid-fire questions and comparisons |
| [LLD Case Studies](LLD-Case-Studies/README.md) | Approach for full design problems (Parking Lot, etc.) |

---

## Recommended Learning Order

1. **Core Concepts** — the vocabulary everything else uses.
   OOP → Class vs Interface vs Abstract Class → Association/Aggregation/Composition → Composition vs Inheritance → Coupling & Cohesion → Programming to Interfaces → Dependency Injection → UML.
2. **SOLID** — in order S → O → L → I → D.
3. **High-frequency patterns** — Singleton, Factory Method, Builder, Strategy, Observer, Decorator, Adapter.
4. **Remaining patterns** — Creational → Structural → Behavioral.
5. **Interview Questions** — self-test with answers hidden.
6. **Case Studies** — apply everything end-to-end.

---

## Quick Revision (Night Before the Interview)

Most-asked topics, in priority order:

- [SOLID overview](SOLID/README.md) — especially [OCP](SOLID/Open-Closed-Principle.md), [LSP](SOLID/Liskov-Substitution-Principle.md), [DIP](SOLID/Dependency-Inversion-Principle.md)
- [Composition vs Inheritance](Core-Concepts/Composition-vs-Inheritance.md)
- [Class vs Interface vs Abstract Class](Core-Concepts/Class-vs-Interface-vs-Abstract-Class.md)
- [Singleton](Design-Patterns/Creational/Singleton.md) (thread safety!)
- [Factory Method](Design-Patterns/Creational/Factory-Method.md) vs [Abstract Factory](Design-Patterns/Creational/Abstract-Factory.md)
- [Builder](Design-Patterns/Creational/Builder.md)
- [Strategy](Design-Patterns/Behavioral/Strategy.md) vs [State](Design-Patterns/Behavioral/State.md)
- [Observer](Design-Patterns/Behavioral/Observer.md)
- [Decorator](Design-Patterns/Structural/Decorator.md) vs [Proxy](Design-Patterns/Structural/Proxy.md) vs [Adapter](Design-Patterns/Structural/Adapter.md)
- [Pattern comparison table](Interview-Questions/Design-Pattern-Questions.md)

---

## Revision Checklist

### Core Concepts
- [ ] [OOP Principles](Core-Concepts/OOP-Principles.md)
- [ ] [Class vs Interface vs Abstract Class](Core-Concepts/Class-vs-Interface-vs-Abstract-Class.md)
- [ ] [Association, Aggregation, Composition](Core-Concepts/Association-Aggregation-Composition.md)
- [ ] [Composition vs Inheritance](Core-Concepts/Composition-vs-Inheritance.md)
- [ ] [Coupling and Cohesion](Core-Concepts/Coupling-and-Cohesion.md)
- [ ] [Programming to Interfaces](Core-Concepts/Programming-to-Interfaces.md)
- [ ] [Dependency Injection](Core-Concepts/Dependency-Injection.md)
- [ ] [UML Class Diagrams](Core-Concepts/UML-Class-Diagrams.md)

### SOLID
- [ ] [Single Responsibility](SOLID/Single-Responsibility-Principle.md)
- [ ] [Open/Closed](SOLID/Open-Closed-Principle.md)
- [ ] [Liskov Substitution](SOLID/Liskov-Substitution-Principle.md)
- [ ] [Interface Segregation](SOLID/Interface-Segregation-Principle.md)
- [ ] [Dependency Inversion](SOLID/Dependency-Inversion-Principle.md)

### Creational Patterns
- [ ] [Singleton](Design-Patterns/Creational/Singleton.md)
- [ ] [Factory Method](Design-Patterns/Creational/Factory-Method.md)
- [ ] [Abstract Factory](Design-Patterns/Creational/Abstract-Factory.md)
- [ ] [Builder](Design-Patterns/Creational/Builder.md)
- [ ] [Prototype](Design-Patterns/Creational/Prototype.md)

### Structural Patterns
- [ ] [Adapter](Design-Patterns/Structural/Adapter.md)
- [ ] [Decorator](Design-Patterns/Structural/Decorator.md)
- [ ] [Facade](Design-Patterns/Structural/Facade.md)
- [ ] [Proxy](Design-Patterns/Structural/Proxy.md)
- [ ] [Composite](Design-Patterns/Structural/Composite.md)
- [ ] [Bridge](Design-Patterns/Structural/Bridge.md)
- [ ] [Flyweight](Design-Patterns/Structural/Flyweight.md)

### Behavioral Patterns
- [ ] [Strategy](Design-Patterns/Behavioral/Strategy.md)
- [ ] [Observer](Design-Patterns/Behavioral/Observer.md)
- [ ] [Command](Design-Patterns/Behavioral/Command.md)
- [ ] [State](Design-Patterns/Behavioral/State.md)
- [ ] [Template Method](Design-Patterns/Behavioral/Template-Method.md)
- [ ] [Iterator](Design-Patterns/Behavioral/Iterator.md)
- [ ] [Chain of Responsibility](Design-Patterns/Behavioral/Chain-of-Responsibility.md)
- [ ] [Mediator](Design-Patterns/Behavioral/Mediator.md)
- [ ] [Memento](Design-Patterns/Behavioral/Memento.md)
- [ ] [Visitor](Design-Patterns/Behavioral/Visitor.md)
- [ ] [Interpreter](Design-Patterns/Behavioral/Interpreter.md)

### Interview Questions
- [ ] [SOLID Questions](Interview-Questions/SOLID-Questions.md)
- [ ] [Design Pattern Questions](Interview-Questions/Design-Pattern-Questions.md)
- [ ] [Core LLD Questions](Interview-Questions/Core-LLD-Questions.md)

---

## Adding a New Topic

1. Pick the right folder (or create one and link it from this README's Table of Contents).
2. Name the file in `Title-Case-With-Hyphens.md`.
3. Copy the template below and fill it in. **Skip sections that don't apply** — no filler.
4. Link the new page from its category `README.md`, add it to the checklist above, and cross-link it from 1–2 related topics.
5. Prefer a link to an existing page over re-explaining a concept.

<details>
<summary><b>Topic template</b> (click to expand)</summary>

````markdown
# Topic Name

> **Category:** (patterns only: Creational / Structural / Behavioral)
> **Intent:** (patterns only: one sentence)

## Quick Summary
- **Definition:** 1–2 sentences.
- **Problem it solves:**
- **Use when:**
- **Avoid when:**

## Key Takeaways
- 3–7 bullets: implementation details, trade-offs, misconceptions.

## Pattern Profile            <!-- patterns only -->
- **Participants:** Role — responsibility
- **Real-world use:**
- **Before:** the problem without the pattern
- **After:** how the pattern improves it
- **Trade-off:**
- **vs. <Closest Pattern>:**

## Java Example
```java
// Minimal, compilable, commented. Include main() when useful.
```

## Interview Questions
**Q1. Question?**
Short answer.

## Common Pitfalls
- 2–4 bullets.

## Related Topics
- [Other Topic](../Folder/Other-Topic.md)
````

</details>

# Design Pattern Interview Questions

Pattern-selection scenarios and comparisons. For pattern-specific Q&A, see each page under [Design Patterns](../Design-Patterns/README.md).

## General
**Q1. What are design patterns, and why use them?**
Proven, named solutions to recurring design problems. They give a shared vocabulary and encode trade-offs. They're not code to paste, and overusing them adds complexity.

**Q2. What are the three GoF categories?**
Creational (object creation), Structural (object composition), Behavioral (object interaction and responsibility).

**Q3. Which patterns have you used in real projects?**
Pick 2–3 you can defend with a concrete problem → pattern → trade-off story. Builder (config objects), Strategy (pricing rules), Observer/events, Decorator (I/O, middleware), and Adapter (third-party SDKs) are safe choices.

**Q4. Can patterns be harmful?**
Yes: pattern-stuffing, premature abstraction, and extra indirection. Singleton is the classic overuse. Apply a pattern only when the problem it solves is present.

## Which Pattern Would You Use?
| Scenario | Pattern | Why |
|---|---|---|
| Multiple payment methods, more added over time | [Strategy](../Design-Patterns/Behavioral/Strategy.md) | Swap algorithms behind one interface |
| Order lifecycle: Placed → Shipped → Delivered → Cancelled | [State](../Design-Patterns/Behavioral/State.md) | Behavior depends on internal state and transitions |
| Notify email, SMS, and analytics when an order is placed | [Observer](../Design-Patterns/Behavioral/Observer.md) | One-to-many event notification |
| Pizza with any combination of toppings priced dynamically | [Decorator](../Design-Patterns/Structural/Decorator.md) | Stackable add-ons without class explosion |
| Integrate a third-party SMS SDK with a different API | [Adapter](../Design-Patterns/Structural/Adapter.md) | Convert interface without changing either side |
| One simple `placeOrder()` over inventory, payment, shipping | [Facade](../Design-Patterns/Structural/Facade.md) | Simplify a subsystem |
| Object with 10 optional config fields, must be immutable | [Builder](../Design-Patterns/Creational/Builder.md) | Readable construction + validation |
| Undo/redo in a text editor | [Command](../Design-Patterns/Behavioral/Command.md) (+ [Memento](../Design-Patterns/Behavioral/Memento.md)) | Requests as objects with undo |
| Expense approval: manager → director → CFO by amount | [Chain of Responsibility](../Design-Patterns/Behavioral/Chain-of-Responsibility.md) | Pass request until someone handles it |
| File system with files and nested folders, compute total size | [Composite](../Design-Patterns/Structural/Composite.md) | Uniform tree treatment |
| Lazy-load a heavy image or add access control | [Proxy](../Design-Patterns/Structural/Proxy.md) | Control access with the same interface |
| Cross-platform UI where all widgets must match the OS | [Abstract Factory](../Design-Patterns/Creational/Abstract-Factory.md) | Consistent product families |
| App-wide config loaded once | [Singleton](../Design-Patterns/Creational/Singleton.md) (or DI-managed instance) | Single shared instance |
| Millions of map markers sharing icons | [Flyweight](../Design-Patterns/Structural/Flyweight.md) | Share intrinsic state |
| Shapes × renderers (vector/raster) varying independently | [Bridge](../Design-Patterns/Structural/Bridge.md) | Avoid M×N subclasses |
| Chat room where users don't reference each other | [Mediator](../Design-Patterns/Behavioral/Mediator.md) | Centralize many-to-many communication |
| Data export pipeline with fixed steps, varying format | [Template Method](../Design-Patterns/Behavioral/Template-Method.md) | Fixed skeleton, variable steps |
| Add tax/export operations to a stable product hierarchy | [Visitor](../Design-Patterns/Behavioral/Visitor.md) | Add operations without changing classes |

## Comparison Cheat Sheet
| Pair | Distinguishing question |
|---|---|
| [Strategy](../Design-Patterns/Behavioral/Strategy.md) vs [State](../Design-Patterns/Behavioral/State.md) | Who changes the behavior? Client (Strategy) vs the object itself on transitions (State) |
| [Strategy](../Design-Patterns/Behavioral/Strategy.md) vs [Template Method](../Design-Patterns/Behavioral/Template-Method.md) | Composition and whole-algorithm swap vs inheritance and step override |
| [Decorator](../Design-Patterns/Structural/Decorator.md) vs [Proxy](../Design-Patterns/Structural/Proxy.md) | Adds behavior (stacked by client) vs controls access (manages subject) |
| [Adapter](../Design-Patterns/Structural/Adapter.md) vs [Decorator](../Design-Patterns/Structural/Decorator.md) | Changes interface vs keeps interface and adds behavior |
| [Adapter](../Design-Patterns/Structural/Adapter.md) vs [Facade](../Design-Patterns/Structural/Facade.md) | Converts one interface vs simplifies a whole subsystem |
| [Adapter](../Design-Patterns/Structural/Adapter.md) vs [Bridge](../Design-Patterns/Structural/Bridge.md) | Retrofitted after the fact vs designed up front |
| [Factory Method](../Design-Patterns/Creational/Factory-Method.md) vs [Abstract Factory](../Design-Patterns/Creational/Abstract-Factory.md) | One product via subclassing vs product families via a factory object |
| [Builder](../Design-Patterns/Creational/Builder.md) vs [Abstract Factory](../Design-Patterns/Creational/Abstract-Factory.md) | Step-by-step single complex object vs immediate family of objects |
| [Composite](../Design-Patterns/Structural/Composite.md) vs [Decorator](../Design-Patterns/Structural/Decorator.md) | Many children (aggregate) vs one wrapped object (enhance) |
| [Observer](../Design-Patterns/Behavioral/Observer.md) vs [Mediator](../Design-Patterns/Behavioral/Mediator.md) | One-way broadcast vs coordinated two-way interaction |
| [Command](../Design-Patterns/Behavioral/Command.md) vs [Memento](../Design-Patterns/Behavioral/Memento.md) (undo) | Inverse operation vs restore snapshot |
| [Chain of Responsibility](../Design-Patterns/Behavioral/Chain-of-Responsibility.md) vs [Decorator](../Design-Patterns/Structural/Decorator.md) | Can stop propagation vs always delegates |
| [Singleton](../Design-Patterns/Creational/Singleton.md) vs [Flyweight](../Design-Patterns/Structural/Flyweight.md) | One instance total vs one per intrinsic state |

## Patterns in the JDK and Spring
**Q5. Name patterns used in the JDK.**
Iterator (`Iterator`), Decorator (`java.io` streams), Adapter (`InputStreamReader`, `Arrays.asList`), Factory Method (`Calendar.getInstance`, `Collection.iterator`), Builder (`StringBuilder`, `HttpRequest.newBuilder`), Flyweight (`Integer.valueOf`), Strategy (`Comparator`), Command (`Runnable`), Template Method (`AbstractList`), Proxy (`java.lang.reflect.Proxy`), Singleton (`Runtime.getRuntime`).

**Q6. Name patterns used in Spring.**
Singleton scope (container-managed), Factory (`BeanFactory`), Proxy (AOP, `@Transactional`), Template Method (`JdbcTemplate`, `RestTemplate`), Observer (`ApplicationEvent`/`@EventListener`), Front Controller (`DispatcherServlet`), Adapter (`HandlerAdapter`), Chain of Responsibility (Security filter chain).

## Related Topics
- [Design Patterns Overview](../Design-Patterns/README.md)
- [SOLID Questions](SOLID-Questions.md)
- [Composition vs Inheritance](../Core-Concepts/Composition-vs-Inheritance.md)

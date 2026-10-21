# Design Patterns

Reusable solutions to recurring design problems, catalogued by the "Gang of Four" (GoF). A pattern is a **vocabulary and a trade-off**, not a template to paste. Use one when you can name the problem it solves in your design.

## Creational — *how objects are created*
| Pattern | Intent | Interview frequency |
|---|---|---|
| [Singleton](Creational/Singleton.md) | Exactly one instance, global access point | ★★★ |
| [Factory Method](Creational/Factory-Method.md) | Let subclasses decide which class to instantiate | ★★★ |
| [Abstract Factory](Creational/Abstract-Factory.md) | Create families of related objects | ★★ |
| [Builder](Creational/Builder.md) | Construct complex objects step by step | ★★★ |
| [Prototype](Creational/Prototype.md) | Create objects by cloning an existing one | ★ |

## Structural — *how objects are composed*
| Pattern | Intent | Interview frequency |
|---|---|---|
| [Adapter](Structural/Adapter.md) | Convert one interface into another | ★★★ |
| [Decorator](Structural/Decorator.md) | Add responsibilities dynamically by wrapping | ★★★ |
| [Facade](Structural/Facade.md) | Simple interface to a complex subsystem | ★★ |
| [Proxy](Structural/Proxy.md) | Placeholder that controls access to an object | ★★ |
| [Composite](Structural/Composite.md) | Treat individual objects and groups uniformly | ★★ |
| [Bridge](Structural/Bridge.md) | Separate abstraction from implementation | ★ |
| [Flyweight](Structural/Flyweight.md) | Share common state among many objects | ★ |

## Behavioral — *how objects interact and divide responsibility*
| Pattern | Intent | Interview frequency |
|---|---|---|
| [Strategy](Behavioral/Strategy.md) | Swap algorithms at runtime | ★★★ |
| [Observer](Behavioral/Observer.md) | Notify dependents of state changes | ★★★ |
| [Command](Behavioral/Command.md) | Encapsulate a request as an object | ★★ |
| [State](Behavioral/State.md) | Change behavior when internal state changes | ★★ |
| [Template Method](Behavioral/Template-Method.md) | Fixed algorithm skeleton, variable steps | ★★ |
| [Iterator](Behavioral/Iterator.md) | Traverse a collection without exposing internals | ★ |
| [Chain of Responsibility](Behavioral/Chain-of-Responsibility.md) | Pass a request along a chain of handlers | ★★ |
| [Mediator](Behavioral/Mediator.md) | Centralize communication between objects | ★ |
| [Memento](Behavioral/Memento.md) | Capture and restore state without breaking encapsulation | ★ |
| [Visitor](Behavioral/Visitor.md) | Add operations to a class hierarchy without changing it | ★ |
| [Interpreter](Behavioral/Interpreter.md) | Evaluate sentences of a simple language | ★ |

## Commonly Confused Pairs
| Pair | Key distinction | 
|---|---|
| Strategy vs State | Client picks strategy; state transitions itself |
| Decorator vs Proxy | Decorator adds behavior; Proxy controls access |
| Adapter vs Facade | Adapter converts one interface; Facade simplifies many |
| Factory Method vs Abstract Factory | One product via inheritance vs a family via composition |
| Template Method vs Strategy | Inheritance (vary steps) vs composition (swap whole algorithm) |
| Composite vs Decorator | Many children (tree) vs exactly one wrapped object |

Full comparison table: [Design Pattern Questions](../Interview-Questions/Design-Pattern-Questions.md).

## How to Use a Pattern in an Interview
1. State the **problem** first ("payment methods will keep growing").
2. Name the pattern and its **participants** in your design.
3. Mention **one trade-off** ("adds a class per method").
4. Don't force patterns. Interviewers penalize pattern-stuffing.

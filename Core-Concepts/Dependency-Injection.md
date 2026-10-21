# Dependency Injection (DI)

## Quick Summary
- **Definition:** A technique where an object **receives** its dependencies from outside rather than creating them itself.
- **Problem it solves:** Hard-wired `new` calls that make classes impossible to unit test or reconfigure.
- **Use when:** A class depends on something volatile or external (DB, HTTP client, clock, payment gateway) or something you'd want to swap.
- **Avoid over-applying when:** Injecting trivial value objects or pure utilities adds ceremony with no benefit.

## Key Takeaways
- Three forms: **constructor injection** (preferred), **setter injection** (optional deps), **field injection** (`@Autowired` on fields; discouraged).
- Constructor injection makes dependencies **explicit**, allows `final` fields (immutability), and guarantees a fully initialized object.
- DI is the **technique**; [DIP](../SOLID/Dependency-Inversion-Principle.md) is the **principle**; **IoC containers** (Spring, Guice) automate the wiring.
- You don't need a framework. Passing objects to constructors in `main()` is DI (the "composition root").
- DI vs **Service Locator**: a locator lets a class *pull* dependencies from a registry, which hides them. DI *pushes* them in, which keeps them visible.

## Java Example
```java
interface Clock { int hour(); }                               // abstraction over a volatile dependency

class SystemClock implements Clock {
    public int hour() { return java.time.LocalTime.now().getHour(); }
}

class Greeter {
    private final Clock clock;                                // final: set once, immutable

    Greeter(Clock clock) { this.clock = clock; }              // constructor injection

    String greet() { return clock.hour() < 12 ? "Good morning" : "Good afternoon"; }
}

class Main {
    public static void main(String[] args) {
        // Composition root: the only place that knows concrete classes
        Greeter real = new Greeter(new SystemClock());
        System.out.println(real.greet());

        // In a unit test: inject a fixed clock for a deterministic result
        Greeter test = new Greeter(() -> 9);                  // lambda as a fake Clock
        System.out.println(test.greet());                     // Good morning
    }
}
```

## Interview Questions
**Q1. Why is constructor injection preferred over field injection?** *(most common)*
Dependencies are explicit and required, fields can be `final`, the object is never half-initialized, and it's testable without a framework (just call the constructor). Field injection hides dependencies and needs reflection.

**Q2. DI vs DIP vs IoC?**
DIP: depend on abstractions (principle). DI: supply dependencies from outside (technique). IoC: a framework controls object creation and lifecycle (broader concept that DI containers implement).

**Q3. When would you use setter injection?**
For optional dependencies with sensible defaults, or to resolve rare circular dependencies (though circular deps usually signal a design problem).

**Q4. DI vs Service Locator?**
A locator hides dependencies inside method bodies (`Locator.get(Repo.class)`) and couples code to the locator. DI exposes them in the constructor signature.

**Q5. How does DI improve testing?**
You can inject fakes/mocks (in-memory repo, fixed clock) instead of real infrastructure, making tests fast and deterministic.

**Q6. What does a "too many constructor parameters" smell indicate?**
The class likely violates SRP. Split it rather than switching to field injection to hide the problem.

## Common Pitfalls
- Thinking DI requires Spring.
- Using field injection, which hides dependencies and blocks immutability.
- Injecting concrete classes instead of abstractions where substitution matters.

## Related Topics
- [Dependency Inversion Principle](../SOLID/Dependency-Inversion-Principle.md)
- [Programming to Interfaces](Programming-to-Interfaces.md)
- [Singleton](../Design-Patterns/Creational/Singleton.md) — DI-managed single instances are the cleaner alternative
- [Coupling and Cohesion](Coupling-and-Cohesion.md)

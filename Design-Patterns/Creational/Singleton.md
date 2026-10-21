# Singleton

> **Category:** Creational
> **Intent:** Ensure a class has exactly one instance and provide a global point of access to it.

## Quick Summary
- **Definition:** A class that controls its own instantiation so only one object ever exists.
- **Problem it solves:** Shared resources that must be unique: configuration, logger, connection pool, cache.
- **Use when:** Exactly one instance is a real domain requirement, and it's needed across the app.
- **Avoid when:** You just want convenient global access. That's global mutable state, and it hurts testability. Prefer one instance managed by [DI](../../Core-Concepts/Dependency-Injection.md).

## Key Takeaways
- Three ingredients: `private` constructor, `private static` instance, `public static` accessor.
- **Thread safety** is the core interview topic: eager init, `synchronized` method, double-checked locking with `volatile`, Bill Pugh holder, enum.
- **Enum singleton** is the most robust: thread-safe, serialization-safe, reflection-safe.
- Can be broken by **reflection**, **serialization** (fix: `readResolve()`), and **cloning**.
- Violates [SRP](../../SOLID/Single-Responsibility-Principle.md) (manages own lifecycle + business logic) and hides dependencies.
- "One per JVM" is really **one per ClassLoader**.

## Pattern Profile
- **Participants:** `Singleton` — holds the sole instance and exposes `getInstance()`.
- **Real-world use:** `Runtime.getRuntime()`, an app-wide config or logger, Spring beans (singleton *scope*, container-managed, not GoF Singleton).
- **Before:** Multiple config objects get created, each reading files and possibly holding inconsistent values.
- **After:** One shared instance, created once, accessed consistently.
- **Trade-off:** Global state makes unit tests order-dependent and hard to mock.
- **vs. Static utility class:** A singleton is an object. It can implement interfaces, be passed around, and be lazily initialized. A static class can't be substituted at all.

## Java Example
```java
// 1) Bill Pugh holder: lazy + thread-safe without synchronization
class AppConfig {
    private AppConfig() { System.out.println("Loading config once"); }  // block external `new`

    private static class Holder {                       // loaded only on first getInstance()
        static final AppConfig INSTANCE = new AppConfig();
    }

    static AppConfig getInstance() { return Holder.INSTANCE; } // JVM guarantees safe init
}

// 2) Double-checked locking: know it for interviews
class Logger {
    private static volatile Logger instance;            // volatile prevents seeing a half-built object

    private Logger() {}

    static Logger getInstance() {
        if (instance == null) {                         // 1st check: skip lock once initialized
            synchronized (Logger.class) {
                if (instance == null) {                 // 2nd check: another thread may have won
                    instance = new Logger();
                }
            }
        }
        return instance;
    }
}

// 3) Enum: simplest and safest (reflection- and serialization-proof)
enum IdGenerator {
    INSTANCE;
    private int next = 0;
    synchronized int nextId() { return ++next; }
}

class Main {
    public static void main(String[] args) {
        System.out.println(AppConfig.getInstance() == AppConfig.getInstance()); // true
        System.out.println(Logger.getInstance() == Logger.getInstance());       // true
        System.out.println(IdGenerator.INSTANCE.nextId());                      // 1
    }
}
```

## Interview Questions
**Q1. How do you make a Singleton thread-safe?** *(most common)*
Eager static init, `synchronized getInstance()` (slow), double-checked locking with `volatile`, Bill Pugh holder class, or enum. Holder or enum are the preferred answers.

**Q2. Why is `volatile` required in double-checked locking?**
`instance = new Logger()` isn't atomic. Without `volatile`, reordering can publish the reference before the constructor finishes, so another thread sees a partially constructed object.

**Q3. How can a Singleton be broken?**
Reflection (`setAccessible(true)` on the constructor), deserialization (creates a new object), cloning, multiple ClassLoaders. Fixes: throw in the constructor if the instance exists, implement `readResolve()`, don't implement `Cloneable`. An enum handles all of them.

**Q4. Singleton vs static class?**
A singleton supports interfaces, polymorphism, lazy init, and can be passed as a dependency. A static class is just a namespace for functions.

**Q5. Why is Singleton called an anti-pattern?**
It's hidden global state: hidden dependencies, tight coupling, hard to mock, and a source of concurrency bugs. Prefer DI-managed single instances.

**Q6. Is a Spring singleton bean a GoF Singleton?**
No. Spring guarantees one instance **per container**, and the class itself has a normal public constructor. That's a scope, not the pattern.

**Q7. Eager vs lazy initialization trade-off?**
Eager is simple and thread-safe but costs startup time even if unused. Lazy defers cost but needs care for thread safety.

## Common Pitfalls
- Writing lazy init without synchronization, or DCL without `volatile`.
- Using Singleton for things that merely "happen to have one instance today".
- Storing mutable request-specific state in a singleton (concurrency bugs).

## Related Topics
- [Dependency Injection](../../Core-Concepts/Dependency-Injection.md) — the preferred alternative
- [Flyweight](../Structural/Flyweight.md) — shares many instances, not one
- [Abstract Factory](Abstract-Factory.md) — concrete factories are often singletons

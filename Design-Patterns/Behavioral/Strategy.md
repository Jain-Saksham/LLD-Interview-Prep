# Strategy

> **Category:** Behavioral
> **Intent:** Define a family of algorithms, encapsulate each one, and make them interchangeable at runtime.

## Quick Summary
- **Definition:** A context delegates part of its behavior to a strategy object behind an interface. Swap the object to swap the algorithm.
- **Problem it solves:** Large `if/else`/`switch` blocks selecting algorithms, which must be edited for every new variant.
- **Use when:** Multiple ways to do one task (pricing, sorting, routing, payment) and the choice varies by client, config, or runtime.
- **Avoid when:** There are only two stable variants that never change. A simple conditional is clearer.

## Key Takeaways
- Composition-based: the context **has-a** strategy (vs [Template Method](Template-Method.md), which uses inheritance).
- Direct application of [OCP](../../SOLID/Open-Closed-Principle.md): new algorithm = new class.
- In Java 8+, a strategy is often just a **lambda** (`Comparator`, `Function`), so no class is needed.
- The **client** chooses the strategy; strategies typically don't know about each other.
- JDK examples: `Comparator` passed to `Collections.sort()`, `ThreadPoolExecutor`'s `RejectedExecutionHandler`.

## Pattern Profile
- **Participants:**
  - `Strategy` (`DiscountStrategy`) — algorithm interface.
  - `ConcreteStrategy` (`NoDiscount`, `FestiveDiscount`) — algorithm implementations.
  - `Context` (`Checkout`) — holds a strategy and delegates to it.
- **Real-world use:** Pricing/discount rules, payment methods, route finding (fastest/shortest), compression formats, sorting comparators.
- **Before:** `if (type == FESTIVE) ... else if (type == PREMIUM) ...` inside `Checkout`, edited for every new rule.
- **After:** `Checkout` calls `strategy.apply(amount)`. New rules plug in without touching it.
- **Trade-off:** Clients must know the strategies exist to choose one; more classes for small algorithms.
- **vs. [State](State.md):** Same structure. In Strategy the **client** chooses the algorithm and strategies are independent. In State, the **state objects themselves** trigger transitions based on internal conditions.

## Java Example
```java
interface DiscountStrategy {                            // Strategy
    double apply(double amount);
}

class NoDiscount implements DiscountStrategy {          // ConcreteStrategy
    public double apply(double amount) { return amount; }
}

class FestiveDiscount implements DiscountStrategy {     // ConcreteStrategy
    public double apply(double amount) { return amount * 0.8; }
}

class Checkout {                                        // Context
    private DiscountStrategy strategy;

    Checkout(DiscountStrategy strategy) { this.strategy = strategy; }
    void setStrategy(DiscountStrategy strategy) { this.strategy = strategy; } // swap at runtime

    double total(double amount) { return strategy.apply(amount); }            // delegate
}

class Main {
    public static void main(String[] args) {
        Checkout checkout = new Checkout(new NoDiscount());
        System.out.println(checkout.total(1000));         // 1000.0

        checkout.setStrategy(new FestiveDiscount());
        System.out.println(checkout.total(1000));         // 800.0

        checkout.setStrategy(amount -> amount - 100);     // lambda as a strategy
        System.out.println(checkout.total(1000));         // 900.0
    }
}
```

## Interview Questions
**Q1. Strategy vs State?** *(most common)*
Same UML. Strategy: the client picks an algorithm, and strategies don't know each other. State: behavior changes as internal state changes, and states often trigger transitions to other states.

**Q2. Strategy vs Template Method?**
Strategy uses composition and swaps the whole algorithm at runtime. Template Method uses inheritance: a fixed skeleton with overridable steps, chosen at compile time.

**Q3. How do lambdas change the Strategy pattern?**
For single-method strategies, a lambda or method reference replaces the concrete class, e.g., `list.sort(Comparator.comparing(User::age))`.

**Q4. Who selects the strategy?**
The client, often through config, a factory, or a `Map<String, Strategy>` registry, which avoids reintroducing the `switch`.

**Q5. How does Strategy relate to SOLID?**
OCP (add algorithms without editing the context), SRP (each algorithm in its own class), DIP (context depends on the interface).

**Q6. Strategy vs Bridge?**
Similar shape. Strategy swaps one behavior; Bridge separates two entire class hierarchies to vary independently.

## Common Pitfalls
- Moving the `switch` into a factory and calling it solved. Use a registry map or DI to avoid scattering it.
- Confusing Strategy with State.
- Creating strategy classes for trivial one-line differences where a lambda suffices.

## Related Topics
- [State](State.md)
- [Template Method](Template-Method.md)
- [Bridge](../Structural/Bridge.md)
- [Open/Closed Principle](../../SOLID/Open-Closed-Principle.md)
- [Command](Command.md)

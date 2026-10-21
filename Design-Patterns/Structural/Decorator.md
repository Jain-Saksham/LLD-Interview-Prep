# Decorator

> **Category:** Structural
> **Intent:** Attach additional responsibilities to an object dynamically by wrapping it in objects with the same interface.

## Quick Summary
- **Definition:** A decorator implements the same interface as the object it wraps, delegates to it, and adds behavior before/after.
- **Problem it solves:** **Subclass explosion** from combining features (`MilkSugarCoffee`, `MilkCaramelCoffee`...).
- **Use when:** You need to mix and match optional behaviors at runtime, or add cross-cutting concerns (logging, caching, compression).
- **Avoid when:** Order-dependent decorator stacks become confusing, or there's only one fixed extension.

## Key Takeaways
- Decorator **is-a** component (same interface) and **has-a** component (wraps one). That's what enables stacking.
- Combinations are built at runtime by composition, not compile-time subclassing. Classic [composition over inheritance](../../Core-Concepts/Composition-vs-Inheritance.md).
- **Order matters**: `Encrypt(Compress(stream))` ≠ `Compress(Encrypt(stream))`.
- Supports [OCP](../../SOLID/Open-Closed-Principle.md) and [SRP](../../SOLID/Single-Responsibility-Principle.md): each feature is its own class.
- JDK example: `new BufferedReader(new InputStreamReader(new FileInputStream(f)))`; `Collections.unmodifiableList()`.

## Pattern Profile
- **Participants:**
  - `Component` (`Coffee`) — common interface.
  - `ConcreteComponent` (`BasicCoffee`) — the base object.
  - `Decorator` (`CoffeeDecorator`) — abstract wrapper holding a `Component`.
  - `ConcreteDecorator` (`Milk`, `Sugar`) — adds behavior.
- **Real-world use:** Java I/O streams, pricing add-ons (toppings), HTTP middleware, adding retry/logging/caching around a service.
- **Before:** A subclass for each combination of features, which grows combinatorially.
- **After:** N features = N decorator classes, combined freely at runtime.
- **Trade-off:** Many small wrapper objects; debugging deep stacks is harder; identity checks (`==`) on the wrapped object fail.
- **vs. [Proxy](Proxy.md):** Both wrap with the same interface. Decorator **adds behavior** and is composed by the client, often stacked. Proxy **controls access** (lazy load, security, caching) and usually manages its subject's lifecycle itself.

## Java Example
```java
interface Coffee {                                      // Component
    double cost();
    String description();
}

class BasicCoffee implements Coffee {                   // ConcreteComponent
    public double cost() { return 50; }
    public String description() { return "Coffee"; }
}

abstract class CoffeeDecorator implements Coffee {      // Decorator: is-a Coffee, has-a Coffee
    protected final Coffee inner;
    CoffeeDecorator(Coffee inner) { this.inner = inner; }
}

class Milk extends CoffeeDecorator {                    // ConcreteDecorator
    Milk(Coffee inner) { super(inner); }
    public double cost() { return inner.cost() + 20; }  // delegate, then add
    public String description() { return inner.description() + " + Milk"; }
}

class Sugar extends CoffeeDecorator {                   // ConcreteDecorator
    Sugar(Coffee inner) { super(inner); }
    public double cost() { return inner.cost() + 5; }
    public String description() { return inner.description() + " + Sugar"; }
}

class Main {
    public static void main(String[] args) {
        Coffee order = new Sugar(new Milk(new Milk(new BasicCoffee()))); // stack at runtime
        System.out.println(order.description() + " = " + order.cost());
        // Coffee + Milk + Milk + Sugar = 95.0
    }
}
```

## Interview Questions
**Q1. Decorator vs inheritance?** *(most common)*
Inheritance fixes behavior at compile time and explodes with combinations. Decorator composes behavior at runtime with one class per feature.

**Q2. Decorator vs Proxy?**
Same structure, different intent. Decorator enhances behavior and is usually stacked by the client. Proxy controls access to the real object, and the client usually doesn't know it's there.

**Q3. Decorator vs Adapter?**
Decorator keeps the interface and adds behavior. Adapter changes the interface.

**Q4. Where is Decorator used in the JDK?**
`java.io`: `BufferedInputStream`, `DataInputStream`, `GZIPInputStream` wrap other streams. Also `Collections.synchronizedList()`.

**Q5. Does the order of decorators matter?**
Yes, when behaviors interact, e.g., compress-then-encrypt vs encrypt-then-compress, or a discount before vs after tax.

**Q6. Downsides?**
Many tiny classes, harder debugging, and the wrapped object's identity is lost (`decorated != original`).

**Q7. Decorator vs Composite?**
Decorator wraps exactly one component to add behavior. Composite holds many children to represent a tree.

## Common Pitfalls
- Forgetting the decorator must implement the **same** interface (otherwise it can't be stacked).
- Not delegating to the wrapped object, which drops the base behavior.
- Using Decorator where a simple flag or Strategy would do.

## Related Topics
- [Proxy](Proxy.md)
- [Adapter](Adapter.md)
- [Composite](Composite.md)
- [Strategy](../Behavioral/Strategy.md) — changes the "guts"; Decorator changes the "skin"
- [Chain of Responsibility](../Behavioral/Chain-of-Responsibility.md)

# Prototype

> **Category:** Creational
> **Intent:** Create new objects by copying an existing instance (the prototype) instead of building from scratch.

## Quick Summary
- **Definition:** Objects expose a `copy()`/`clone()` method; new instances are made by cloning a pre-configured prototype.
- **Problem it solves:** Expensive or complex initialization (DB lookups, parsing, many config steps) repeated for similar objects; or creating copies without knowing the concrete class.
- **Use when:** Object creation is costly and instances differ only slightly; or you need copies of objects known only by interface.
- **Avoid when:** Objects are cheap to construct or have complex graphs (circular refs) that make deep copying error-prone.

## Key Takeaways
- **Shallow vs deep copy** is the #1 topic: shallow copies share mutable fields, so a change to one affects the other.
- Java's `Cloneable`/`Object.clone()` is widely considered **broken** (marker interface, no public `clone()` in the interface, checked exception, bypasses constructors). Prefer a **copy constructor** or a `copy()` method.
- A **prototype registry** stores ready-made prototypes keyed by name.
- Client clones via the interface, so it doesn't need to know the concrete class.

## Pattern Profile
- **Participants:**
  - `Prototype` (`Shape`) — declares `copy()`.
  - `ConcretePrototype` (`Circle`) — implements copying itself.
  - `Client` / `Registry` — asks a prototype to copy itself.
- **Real-world use:** Game enemies spawned from templates, document templates, caching pre-loaded config objects, spreadsheet "duplicate row".
- **Before:** Every new object re-runs costly setup, or the client needs `if (shape instanceof Circle) new Circle(...)` to duplicate.
- **After:** `prototype.copy()` produces a correct duplicate polymorphically.
- **Trade-off:** Deep copy of complex object graphs is hard to get right and maintain.
- **vs. [Factory Method](Factory-Method.md):** A factory builds new objects from scratch via subclasses. Prototype builds them from an existing instance, with no subclass per product needed.

## Java Example
```java
import java.util.ArrayList;
import java.util.List;

interface Shape {                                    // Prototype
    Shape copy();
}

class Circle implements Shape {                      // ConcretePrototype
    int radius;
    List<String> tags;                               // mutable field → needs deep copy

    Circle(int radius, List<String> tags) { this.radius = radius; this.tags = tags; }

    private Circle(Circle source) {                  // copy constructor
        this.radius = source.radius;
        this.tags = new ArrayList<>(source.tags);    // deep copy: don't share the list
    }

    public Shape copy() { return new Circle(this); }

    @Override public String toString() { return "Circle(" + radius + ", " + tags + ")"; }
}

class Main {
    public static void main(String[] args) {
        Circle original = new Circle(10, new ArrayList<>(List.of("red")));
        Circle clone = (Circle) original.copy();
        clone.tags.add("dashed");                    // modifies only the clone

        System.out.println(original);                // Circle(10, [red])
        System.out.println(clone);                   // Circle(10, [red, dashed])
    }
}
```

## Interview Questions
**Q1. Shallow copy vs deep copy?** *(most common)*
A shallow copy duplicates field values, so references point to the same nested objects. A deep copy also duplicates the nested mutable objects. Immutable fields (like `String`) can safely be shared.

**Q2. Why is Java's `Cloneable` considered flawed?**
It's a marker interface without a `clone()` method, `Object.clone()` is protected and throws a checked exception, it bypasses constructors, and it's shallow by default. Copy constructors or static factories are cleaner.

**Q3. When is Prototype better than `new`?**
When initialization is expensive (I/O, heavy computation) or when you must copy an object known only through an interface.

**Q4. What is a prototype registry?**
A map of named, pre-configured prototypes (`"goblin" → Enemy`) that clients clone instead of configuring from scratch.

**Q5. What happens with circular references during deep copy?**
Naive recursion loops forever. You need a visited map (original → copy) to handle cycles.

## Common Pitfalls
- Returning a shallow copy with shared mutable collections, which causes spooky side effects.
- Defaulting to `Cloneable` without knowing its problems.
- Using Prototype where construction is cheap.

## Related Topics
- [Factory Method](Factory-Method.md)
- [Memento](../Behavioral/Memento.md) — also copies state, but to restore it later
- [Flyweight](../Structural/Flyweight.md) — shares instead of copying

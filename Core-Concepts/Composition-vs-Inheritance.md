# Composition vs Inheritance

## Quick Summary
- **Definition:** **Inheritance** reuses behavior by extending a class ("is-a"). **Composition** reuses behavior by holding a reference to another object and delegating to it ("has-a").
- **Problem it solves:** Choosing the right reuse mechanism. Inheritance misuse leads to fragile, rigid hierarchies.
- **Use inheritance when:** There's a true, stable **is-a** relationship and the subclass honors the full parent contract ([LSP](../SOLID/Liskov-Substitution-Principle.md)).
- **Use composition when:** You want to reuse behavior, combine behaviors, or swap them at runtime. This is the default choice.

## Key Takeaways
- **"Favor composition over inheritance"** (GoF, Effective Java Item 18), not "never use inheritance".
- Inheritance is **white-box** reuse: subclasses depend on parent internals (**fragile base class problem**).
- Composition is **black-box** reuse: only the public interface is used, and parts can be swapped at runtime.
- Inheritance is fixed at **compile time**; composition can change at **runtime**.
- Combining features via inheritance causes **class explosion**; composition scales linearly ([Decorator](../Design-Patterns/Structural/Decorator.md), [Strategy](../Design-Patterns/Behavioral/Strategy.md)).
- Java allows only single class inheritance, but you can compose many objects.

| | Inheritance | Composition |
|---|---|---|
| Relationship | is-a | has-a |
| Coupling | Tight (to parent internals) | Loose (to interface) |
| Flexibility | Compile-time | Runtime swappable |
| Reuse | Automatic (all parent methods) | Explicit delegation |
| Risk | Fragile base class, LSP violations | More boilerplate (forwarding methods) |

## Java Example
```java
import java.util.ArrayList;
import java.util.Collection;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

// Inheritance pitfall (Effective Java Item 18): counting additions to a HashSet
class CountingSetInherited<E> extends HashSet<E> {
    int addCount = 0;
    @Override public boolean add(E e) { addCount++; return super.add(e); }
    @Override public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);          // HashSet.addAll() internally calls add() → double count!
    }
}

// Composition: wrap and delegate, independent of the wrapped class's internals
class CountingSet<E> {
    private final Set<E> set = new HashSet<>();   // has-a
    int addCount = 0;
    boolean add(E e) { addCount++; return set.add(e); }
    boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return set.addAll(c);            // set's internals can't affect our count
    }
}

class Main {
    public static void main(String[] args) {
        List<String> items = new ArrayList<>(List.of("a", "b", "c"));

        CountingSetInherited<String> s1 = new CountingSetInherited<>();
        s1.addAll(items);
        System.out.println("Inherited: " + s1.addCount);   // 6, wrong (fragile base class)

        CountingSet<String> s2 = new CountingSet<>();
        s2.addAll(items);
        System.out.println("Composed: " + s2.addCount);    // 3, correct
    }
}
```

## Interview Questions
**Q1. Why favor composition over inheritance?** *(most common)*
Looser coupling (depends on the interface, not internals), runtime flexibility, no fragile base class problem, no class explosion, and easier testing via swappable parts.

**Q2. When is inheritance the right choice?**
True is-a relationships with a stable parent designed for extension (e.g., Template Method, framework base classes), where the subclass fully honors LSP.

**Q3. What is the fragile base class problem?**
Changes to a parent's implementation silently break subclasses that relied on its internal behavior, as in the `HashSet.addAll()` example.

**Q4. Which design patterns are built on composition?**
Strategy, Decorator, Bridge, Composite, State, Observer, Proxy, Adapter (object adapter). Template Method and Factory Method rely on inheritance.

**Q5. What's the downside of composition?**
More boilerplate (forwarding methods) and more objects to wire. Inheritance gives you parent methods for free.

**Q6. How do you design a class for inheritance safely?**
Document self-use of overridable methods, make the class/methods `final` unless explicitly designed for extension, and avoid calling overridable methods from constructors.

## Common Pitfalls
- Using inheritance purely to reuse code when no is-a relationship exists (e.g., `Stack extends Vector` in the JDK).
- Claiming "inheritance is always bad". Interviewers want nuance.
- Forgetting to mention runtime swappability as composition's key advantage.

## Related Topics
- [Association, Aggregation, Composition](Association-Aggregation-Composition.md) — composition as a UML relationship
- [Liskov Substitution Principle](../SOLID/Liskov-Substitution-Principle.md)
- [Decorator](../Design-Patterns/Structural/Decorator.md)
- [Strategy](../Design-Patterns/Behavioral/Strategy.md)
- [Template Method](../Design-Patterns/Behavioral/Template-Method.md)

# Liskov Substitution Principle (LSP)

## Quick Summary
- **Definition:** Objects of a subtype must be **substitutable** for objects of the base type **without breaking the program's correctness**.
- **Plain English:** If code works with a `Bird`, it must keep working when handed any subclass of `Bird`. A subclass may do more, never less, and must not surprise callers.
- **Problem it solves:** Polymorphism that silently breaks — callers needing `instanceof` checks or catching unexpected exceptions.

## Key Takeaways
- It's about **behavior (contracts)**, not just method signatures. The compiler can't check it.
- Subtype rules: **preconditions can't be strengthened, postconditions can't be weakened, invariants must be preserved**.
- Red flags: overrides that throw `UnsupportedOperationException`, empty overrides, `instanceof` in callers.
- "Is-a" in the real world ≠ "is-substitutable-for" in code (Square/Rectangle, Penguin/Bird).
- Fix by **reshaping the hierarchy** (smaller interfaces) or using [composition](../Core-Concepts/Composition-vs-Inheritance.md).

## Violation
```java
class Bird {
    void fly() { System.out.println("Flying"); }
}

class Penguin extends Bird {
    @Override
    void fly() { throw new UnsupportedOperationException("Penguins can't fly"); } // breaks callers
}

class Main {
    static void migrate(Bird b) { b.fly(); }       // valid for Bird, crashes for Penguin
    public static void main(String[] args) {
        migrate(new Bird());
        migrate(new Penguin());                     // runtime exception — LSP violated
    }
}
```

## Corrected Design
```java
abstract class Bird {
    abstract String name();
}

interface Flyable {                  // only birds that can fly promise to fly
    void fly();
}

class Sparrow extends Bird implements Flyable {
    String name() { return "Sparrow"; }
    public void fly() { System.out.println("Sparrow flying"); }
}

class Penguin extends Bird {          // no false promise to fly
    String name() { return "Penguin"; }
}

class Main {
    static void migrate(Flyable f) { f.fly(); }     // only accepts things that truly fly
    public static void main(String[] args) {
        migrate(new Sparrow());
        // migrate(new Penguin());                   // now a compile error, not a runtime bug
    }
}
```

**Key benefit:** Polymorphic code stays correct for every subtype — no type checks, no surprises.

## Interview Questions
**Q1. Explain the Square–Rectangle problem.** *(most common)*
If `Square extends Rectangle`, `setWidth()` must also change height to stay square. Code that sets width=5, height=4 and expects area 20 gets 16. Square can't honor Rectangle's contract, so it violates LSP. Fix: don't make them parent/child; use a common `Shape` with `area()`.

**Q2. How can a subclass violate LSP without throwing?**
By weakening guarantees: returning `null` where the base never did, ignoring a parameter, adding side effects, or requiring stricter input.

**Q3. What are the contract rules for subtypes?**
Preconditions no stronger, postconditions no weaker, invariants preserved, no new exceptions the caller doesn't expect.

**Q4. How do you fix an LSP violation?**
Split the abstraction (ISP), move the varying capability into a separate interface, or replace inheritance with composition.

**Q5. Is LSP only about inheritance?**
No. It applies to any interface implementation: every `List` implementation must behave like a `List`.

**Q6. Does Java's `Collections.unmodifiableList()` violate LSP?**
Arguably yes. It's a `List` whose `add()` throws. The JDK documents those methods as "optional operations", which is a pragmatic compromise.

## Common Pitfalls
- Checking only method signatures and ignoring behavioral contracts.
- Modeling real-world taxonomy directly as an inheritance tree.
- "Fixing" violations with `instanceof` checks in callers, which also breaks OCP.

## Related Topics
- [Interface Segregation Principle](Interface-Segregation-Principle.md)
- [Composition vs Inheritance](../Core-Concepts/Composition-vs-Inheritance.md)
- [Open/Closed Principle](Open-Closed-Principle.md)

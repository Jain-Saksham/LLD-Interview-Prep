# OOP Principles

## Quick Summary
- **Definition:** The four pillars of object-oriented programming: **Encapsulation, Abstraction, Inheritance, Polymorphism**.
- **Problem they solve:** Managing complexity by bundling data with behavior, hiding details, reusing code, and writing code that works with many types.
- **Use when:** Modeling domains with entities that have state and behavior, which is every LLD interview.

## Key Takeaways
| Pillar | What it means | How Java does it |
|---|---|---|
| **Encapsulation** | Bundle data + methods; hide internal state; expose controlled operations | `private` fields, methods that enforce invariants |
| **Abstraction** | Expose *what* an object does, hide *how* | Interfaces, abstract classes, well-named public methods |
| **Inheritance** | Derive a class from another to reuse/extend behavior ("is-a") | `extends`, `implements` |
| **Polymorphism** | One interface, many implementations; the call resolves to the actual object's method | Overriding (runtime), overloading (compile time) |

- **Encapsulation ≠ getters/setters for every field.** Real encapsulation exposes behavior (`withdraw()`), not raw state (`setBalance()`).
- **Abstraction vs encapsulation:** abstraction is a *design* concern (what to expose); encapsulation is an *implementation* mechanism (how to hide).
- **Runtime polymorphism** (dynamic dispatch via overriding) is what enables [OCP](../SOLID/Open-Closed-Principle.md) and most design patterns.
- Inheritance is the most overused pillar. See [Composition vs Inheritance](Composition-vs-Inheritance.md).

## Java Example
```java
abstract class Account {                                   // Abstraction: what an account does
    private double balance;                                // Encapsulation: hidden state

    void deposit(double amt) {
        if (amt <= 0) throw new IllegalArgumentException("Invalid amount");
        balance += amt;                                    // invariant enforced here
    }

    void withdraw(double amt) {
        if (amt > balance + overdraftLimit()) throw new IllegalStateException("Insufficient funds");
        balance -= amt;
    }

    double getBalance() { return balance; }
    abstract double overdraftLimit();                      // subclasses decide
}

class SavingsAccount extends Account {                     // Inheritance
    double overdraftLimit() { return 0; }
}

class CurrentAccount extends Account {                     // Inheritance
    double overdraftLimit() { return 5000; }               // Polymorphism: different behavior
}

class Main {
    public static void main(String[] args) {
        Account[] accounts = { new SavingsAccount(), new CurrentAccount() };
        for (Account a : accounts) {
            a.deposit(1000);
            try {
                a.withdraw(3000);                          // same call, behavior depends on type
                System.out.println(a.getClass().getSimpleName() + " ok: " + a.getBalance());
            } catch (IllegalStateException e) {
                System.out.println(a.getClass().getSimpleName() + " rejected");
            }
        }
    }
}
```

## Interview Questions
**Q1. Difference between abstraction and encapsulation?** *(most common)*
Abstraction hides complexity by exposing only relevant operations (design level: *what*). Encapsulation hides internal state and protects invariants (implementation level: *how*). A `List` interface is abstraction; `ArrayList`'s private array is encapsulation.

**Q2. Compile-time vs runtime polymorphism?**
Compile-time = method **overloading** (resolved by parameter types at compile time). Runtime = method **overriding** (resolved by the actual object type via dynamic dispatch).

**Q3. Does a class with getters and setters for every field have good encapsulation?**
No. It exposes state and lets callers break invariants. Expose meaningful operations instead.

**Q4. Why doesn't Java support multiple inheritance of classes?**
To avoid the diamond problem (ambiguous inherited state/implementations). Java allows multiple interfaces; default-method conflicts must be resolved explicitly.

**Q5. Can you override static or private methods?**
No. Static methods are hidden, not overridden (resolved by reference type). Private methods aren't inherited.

**Q6. What's method hiding vs overriding?**
A static method with the same signature in a subclass *hides* the parent's. The call is chosen by the reference type, not the object type.

## Common Pitfalls
- Defining encapsulation as "private fields + getters/setters".
- Calling overloading "runtime polymorphism".
- Defaulting to inheritance for code reuse.

## Related Topics
- [Class vs Interface vs Abstract Class](Class-vs-Interface-vs-Abstract-Class.md)
- [Composition vs Inheritance](Composition-vs-Inheritance.md)
- [Liskov Substitution Principle](../SOLID/Liskov-Substitution-Principle.md)

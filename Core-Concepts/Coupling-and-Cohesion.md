# Coupling and Cohesion

## Quick Summary
- **Coupling:** How much one module **depends on** another. Aim for **low** (loose) coupling.
- **Cohesion:** How closely the responsibilities **within** a module relate to each other. Aim for **high** cohesion.
- **Problem it solves:** Gives you a vocabulary for judging designs: low coupling + high cohesion = code that's easy to change, test, and reuse.
- **Rule of thumb:** Things that change together belong together (cohesion); things that change independently should be separated (low coupling).

## Key Takeaways
- They're related: splitting a low-cohesion class often reduces coupling for its clients.
- **Tight coupling signs:** `new ConcreteClass()` everywhere, reaching into other objects' internals (`a.getB().getC().doX()`), shared global state, a change in one class forcing changes in many.
- **Low cohesion signs:** "Manager"/"Util"/"Helper" classes with unrelated methods; methods that use disjoint sets of fields.
- Coupling can't be zero; objects must collaborate. Couple to **stable abstractions** ([DIP](../SOLID/Dependency-Inversion-Principle.md)), not volatile details.
- **Law of Demeter** ("talk only to your immediate friends") reduces coupling: avoid long method chains through other objects.
- SOLID mapping: [SRP](../SOLID/Single-Responsibility-Principle.md) and [ISP](../SOLID/Interface-Segregation-Principle.md) → high cohesion; [DIP](../SOLID/Dependency-Inversion-Principle.md) and [OCP](../SOLID/Open-Closed-Principle.md) → low coupling.

## Java Example
```java
// Tight coupling + Law of Demeter violation: Checkout digs into Customer's internals
class Wallet { double balance = 500; }
class Customer { Wallet wallet = new Wallet(); }

class CheckoutTight {
    void charge(Customer c, double amt) {
        c.wallet.balance -= amt;              // knows Customer has a Wallet and how it works
    }
}

// Loose coupling + high cohesion: Customer owns its payment logic
class Account {
    private double balance = 500;
    boolean debit(double amt) {               // cohesive: balance rules live with balance
        if (amt > balance) return false;
        balance -= amt;
        return true;
    }
}

class Shopper {
    private final Account account = new Account();
    boolean pay(double amt) { return account.debit(amt); }   // tells, doesn't expose
}

class CheckoutLoose {
    void charge(Shopper s, double amt) {
        System.out.println(s.pay(amt) ? "Paid" : "Declined");  // depends only on pay()
    }
}

class Main {
    public static void main(String[] args) {
        new CheckoutLoose().charge(new Shopper(), 200);    // Paid
        new CheckoutLoose().charge(new Shopper(), 900);    // Declined
    }
}
```

## Interview Questions
**Q1. Why do we want low coupling and high cohesion?** *(most common)*
Low coupling localizes the impact of change and enables independent testing/reuse. High cohesion makes modules understandable and focused, so related changes happen in one place.

**Q2. How do you reduce coupling?**
Program to interfaces, inject dependencies, follow the Law of Demeter, use events/mediators for cross-module communication, and avoid shared mutable globals.

**Q3. What is the Law of Demeter?**
A method should only call methods on itself, its parameters, objects it creates, and its direct fields. Avoid `a.getB().getC().do()` ("train wrecks"). Prefer "tell, don't ask".

**Q4. Can a design have too little coupling?**
Excessive indirection (interfaces and events everywhere) makes code hard to follow. Coupling to stable things like `String` or a domain value object is fine.

**Q5. How do SOLID principles relate?**
SRP/ISP raise cohesion; DIP/OCP lower coupling.

**Q6. Which patterns reduce coupling?**
[Observer](../Design-Patterns/Behavioral/Observer.md), [Mediator](../Design-Patterns/Behavioral/Mediator.md), [Facade](../Design-Patterns/Structural/Facade.md), [Strategy](../Design-Patterns/Behavioral/Strategy.md), [Dependency Injection](Dependency-Injection.md).

## Common Pitfalls
- Mixing up the two terms (cohesion is *within*, coupling is *between*).
- Claiming the goal is zero coupling.
- Equating "more classes" with "better cohesion".

## Related Topics
- [Single Responsibility Principle](../SOLID/Single-Responsibility-Principle.md)
- [Dependency Inversion Principle](../SOLID/Dependency-Inversion-Principle.md)
- [Programming to Interfaces](Programming-to-Interfaces.md)
- [Dependency Injection](Dependency-Injection.md)

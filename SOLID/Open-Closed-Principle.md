# Open/Closed Principle (OCP)

## Quick Summary
- **Definition:** Software entities should be **open for extension, closed for modification**.
- **Plain English:** Add new behavior by adding new code (a new class), not by editing tested code.
- **Problem it solves:** Every new variant forces edits to a central `if/else` or `switch`, risking regressions.
- **Avoid over-applying when:** The variation is hypothetical. Add the abstraction when the second or third variant actually appears.

## Key Takeaways
- Achieved through **polymorphism**: depend on an abstraction, add new implementations.
- A `switch` on type that appears in **multiple places** is the classic smell.
- "Closed" is relative — you choose which axis of change to protect against.
- Some modification is unavoidable (e.g., wiring a new class in a factory or config); the goal is to keep **business logic** stable.
- [Strategy](../Design-Patterns/Behavioral/Strategy.md), [Decorator](../Design-Patterns/Structural/Decorator.md), [Template Method](../Design-Patterns/Behavioral/Template-Method.md), and [Observer](../Design-Patterns/Behavioral/Observer.md) are OCP in action.

## Violation
```java
// Adding "UPI" means editing this method and re-testing all existing branches.
class PaymentProcessor {
    void pay(String type, double amount) {
        if (type.equals("CARD")) {
            System.out.println("Card: " + amount);
        } else if (type.equals("PAYPAL")) {
            System.out.println("PayPal: " + amount);
        } else {
            throw new IllegalArgumentException("Unknown type " + type);
        }
    }
}
```

## Corrected Design
```java
interface PaymentMethod {                      // the stable abstraction
    void pay(double amount);
}

class CardPayment implements PaymentMethod {
    public void pay(double amount) { System.out.println("Card: " + amount); }
}

class UpiPayment implements PaymentMethod {    // new variant = new class, no edits elsewhere
    public void pay(double amount) { System.out.println("UPI: " + amount); }
}

class PaymentProcessor {                       // closed for modification
    void process(PaymentMethod method, double amount) { method.pay(amount); }
}

class Main {
    public static void main(String[] args) {
        PaymentProcessor processor = new PaymentProcessor();
        processor.process(new CardPayment(), 100);
        processor.process(new UpiPayment(), 250);
    }
}
```

**Key benefit:** New features don't risk breaking existing, tested code.

## Interview Questions
**Q1. How do you achieve OCP in Java?** *(most common)*
Program to an interface/abstract class and add new implementations; inject them via constructor or factory.

**Q2. Is a `switch` statement always an OCP violation?**
No. A single `switch` in a factory that maps input to objects is fine. Repeated type switches scattered across business logic are the problem.

**Q3. Can you be 100% closed?**
No. You protect against the changes you anticipate. Guessing every axis leads to over-engineering.

**Q4. Which design patterns support OCP?**
Strategy, Decorator, Observer, Template Method, Chain of Responsibility, Visitor (for operations).

**Q5. How does OCP relate to LSP and DIP?**
OCP is the goal; DIP lets high-level code depend on an abstraction; LSP guarantees new subtypes plug in without breaking callers.

**Q6. What's the cost of OCP?**
More types and indirection. Premature abstractions are hard to remove and confuse readers.

## Common Pitfalls
- Introducing interfaces for things that have exactly one implementation and no realistic second one.
- Claiming OCP is met while the factory/registry still needs scattered edits in multiple files.
- Confusing OCP with "never edit code" — bug fixes and refactors are fine.

## Related Topics
- [Strategy](../Design-Patterns/Behavioral/Strategy.md)
- [Decorator](../Design-Patterns/Structural/Decorator.md)
- [Liskov Substitution Principle](Liskov-Substitution-Principle.md)
- [Programming to Interfaces](../Core-Concepts/Programming-to-Interfaces.md)

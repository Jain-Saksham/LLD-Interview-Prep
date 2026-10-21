# Dependency Inversion Principle (DIP)

## Quick Summary
- **Definition:** (1) High-level modules should not depend on low-level modules; both should depend on **abstractions**. (2) Abstractions should not depend on details; details depend on abstractions.
- **Plain English:** Business logic shouldn't `new` up a MySQL class. It should depend on an interface like `OrderRepository`, and the MySQL class implements that interface.
- **Problem it solves:** Business logic welded to infrastructure, which makes it hard to test, swap, or reuse.
- **Avoid over-applying when:** The dependency is a stable value type or utility (`String`, `List`, `Math`). Not everything needs an interface.

## Key Takeaways
- "Inversion": source-code dependency points **against** the flow of control. High-level code owns the interface; low-level code implements it.
- The abstraction should be shaped by the **high-level** client's needs, not mirror the low-level API.
- **DIP ≠ DI.** DIP is the principle; [Dependency Injection](../Core-Concepts/Dependency-Injection.md) is a technique to supply the implementation.
- Biggest practical win: **unit testing** with fakes/mocks.
- Creational patterns ([Factory Method](../Design-Patterns/Creational/Factory-Method.md), [Abstract Factory](../Design-Patterns/Creational/Abstract-Factory.md)) and DI containers handle the wiring.

## Violation
```java
class MySqlDatabase {
    void save(String data) { System.out.println("MySQL: " + data); }
}

class OrderService {
    private final MySqlDatabase db = new MySqlDatabase(); // high-level hard-wired to low-level detail

    void placeOrder(String order) { db.save(order); }
}
```

## Corrected Design
```java
interface OrderRepository {                       // abstraction owned by the high-level module
    void save(String order);
}

class MySqlOrderRepository implements OrderRepository {   // detail depends on abstraction
    public void save(String order) { System.out.println("MySQL: " + order); }
}

class InMemoryOrderRepository implements OrderRepository { // e.g., for tests
    public void save(String order) { System.out.println("Memory: " + order); }
}

class OrderService {
    private final OrderRepository repo;

    OrderService(OrderRepository repo) { this.repo = repo; } // injected, not created

    void placeOrder(String order) { repo.save(order); }
}

class Main {
    public static void main(String[] args) {
        new OrderService(new MySqlOrderRepository()).placeOrder("order-1");
        new OrderService(new InMemoryOrderRepository()).placeOrder("order-2");
    }
}
```

**Key benefit:** Business logic is decoupled from infrastructure, so it's swappable and unit-testable.

## Interview Questions
**Q1. What's the difference between DIP, DI, and IoC?** *(most common)*
DIP is a design principle (depend on abstractions). DI is a technique (pass dependencies in from outside). IoC is the broader idea that a framework/container controls object creation and flow. Spring uses IoC + DI to help you follow DIP.

**Q2. What exactly is "inverted"?**
The direction of source-code dependency. Without DIP, high-level → low-level. With DIP, both point to an abstraction that the high-level module owns.

**Q3. Who should own the interface?**
The high-level (client) module. Defining it in the low-level package keeps the dependency pointing the wrong way.

**Q4. Should every class have an interface?**
No. Use abstractions at volatile boundaries: DB, network, clock, third-party APIs, swappable policies.

**Q5. How does DIP improve testability?**
You can inject an in-memory or mock implementation instead of a real DB or network client.

**Q6. How does DIP support OCP?**
Since high-level code only knows the interface, new implementations can be added without modifying it.

## Common Pitfalls
- Equating DIP with "use Spring" or with DI.
- Creating `IFoo`/`FooImpl` pairs for everything, including stable internals.
- Designing the interface around the low-level API (e.g., `executeSql()`) so the abstraction still leaks details.

## Related Topics
- [Dependency Injection](../Core-Concepts/Dependency-Injection.md)
- [Programming to Interfaces](../Core-Concepts/Programming-to-Interfaces.md)
- [Open/Closed Principle](Open-Closed-Principle.md)
- [Factory Method](../Design-Patterns/Creational/Factory-Method.md)

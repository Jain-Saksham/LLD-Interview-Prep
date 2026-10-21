# Factory Method

> **Category:** Creational
> **Intent:** Define an interface for creating an object, but let subclasses decide which class to instantiate.

## Quick Summary
- **Definition:** A creator class declares an abstract "factory method" that returns a product interface. Subclasses override it to return concrete products.
- **Problem it solves:** Client code full of `new ConcreteX()` that must change whenever a new product type is added.
- **Use when:** A class can't anticipate the exact type it must create, or you want subclasses/config to choose it.
- **Avoid when:** There's only one product type with no realistic variation. A plain constructor is clearer.

## Key Takeaways
- The creator usually has **business logic that uses** the product. The factory method is just one step of it.
- Relies on **inheritance**: each concrete creator overrides one method.
- The common **"Simple Factory"** (static method with a `switch`) is *not* GoF Factory Method, but it's widely used and fine for interviews if you name it correctly.
- Supports [OCP](../../SOLID/Open-Closed-Principle.md) and [DIP](../../SOLID/Dependency-Inversion-Principle.md): the client depends on the product interface only.
- JDK examples: `Calendar.getInstance()`, `NumberFormat.getInstance()`, `List.of()` (static factories); `Collection.iterator()` (true factory method).

## Pattern Profile
- **Participants:**
  - `Product` (`Notification`) — interface of created objects.
  - `ConcreteProduct` (`EmailNotification`, `SmsNotification`) — actual implementations.
  - `Creator` (`NotificationService`) — declares the factory method and uses the product.
  - `ConcreteCreator` (`EmailService`, `SmsService`) — overrides the factory method.
- **Real-world use:** Notification channels, document types in an editor, DB connection types.
- **Before:** `if (type == EMAIL) new Email... else if (type == SMS) ...` scattered through client code.
- **After:** Each creator subclass encapsulates one product choice. Adding a channel = new product + new creator.
- **Trade-off:** Parallel class hierarchies; one creator subclass per product can be heavy.
- **vs. [Abstract Factory](Abstract-Factory.md):** Factory Method creates **one** product via **inheritance**. Abstract Factory creates a **family** of related products via a composed factory **object**.

## Java Example
```java
interface Notification {                                 // Product
    void send(String msg);
}

class EmailNotification implements Notification {        // ConcreteProduct
    public void send(String msg) { System.out.println("Email: " + msg); }
}

class SmsNotification implements Notification {          // ConcreteProduct
    public void send(String msg) { System.out.println("SMS: " + msg); }
}

abstract class NotificationService {                     // Creator
    protected abstract Notification createNotification(); // the factory method

    void notifyUser(String msg) {                         // business logic uses the product
        Notification n = createNotification();            // doesn't know the concrete type
        n.send(msg);
    }
}

class EmailService extends NotificationService {         // ConcreteCreator
    protected Notification createNotification() { return new EmailNotification(); }
}

class SmsService extends NotificationService {           // ConcreteCreator
    protected Notification createNotification() { return new SmsNotification(); }
}

class Main {
    public static void main(String[] args) {
        NotificationService service = new SmsService();  // choose once, e.g., from config
        service.notifyUser("Your OTP is 4821");
    }
}
```

## Interview Questions
**Q1. Factory Method vs Simple Factory?** *(most common)*
Simple Factory is one class with a (often static) method containing a `switch`; adding a type means editing it. Factory Method uses subclass overriding, so adding a type means adding a subclass.

**Q2. Factory Method vs Abstract Factory?**
One product vs a family of related products; inheritance vs composition. Abstract Factory is often implemented using factory methods.

**Q3. Why not just use `new`?**
`new` binds the client to a concrete class. A factory lets you change, cache, or pool the returned object without touching callers.

**Q4. Which SOLID principles does it support?**
OCP (add types without editing client code) and DIP (client depends on the `Product` interface).

**Q5. Give JDK examples.**
`Collection.iterator()` (each collection returns its own iterator), `Calendar.getInstance()`, `NumberFormat.getInstance()`.

**Q6. What are static factory methods (Effective Java Item 1)?**
Named static methods like `valueOf()` or `of()` instead of constructors. They can have descriptive names, return cached instances, or return subtypes. Related idea, but not the GoF pattern.

## Common Pitfalls
- Presenting a static `switch` factory as "Factory Method" without acknowledging the distinction.
- Forgetting the creator usually *uses* the product. It's not just a creation wrapper.
- Adding a factory for objects that never vary.

## Related Topics
- [Abstract Factory](Abstract-Factory.md)
- [Template Method](../Behavioral/Template-Method.md) — factory methods are often a step in one
- [Dependency Inversion Principle](../../SOLID/Dependency-Inversion-Principle.md)

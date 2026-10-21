# Facade

> **Category:** Structural
> **Intent:** Provide a unified, simplified interface to a set of interfaces in a subsystem.

## Quick Summary
- **Definition:** A single class that hides a complex subsystem behind a few high-level methods.
- **Problem it solves:** Clients coupled to many subsystem classes and needing to know the correct call order.
- **Use when:** A subsystem is complex and most clients need only a common workflow.
- **Avoid when:** It would just forward one-to-one calls (pointless layer) or grow into a god class.

## Key Takeaways
- Facade **doesn't hide** the subsystem. Advanced clients can still use subsystem classes directly.
- Reduces [coupling](../../Core-Concepts/Coupling-and-Cohesion.md): clients depend on one class instead of many.
- Encapsulates **orchestration/ordering** knowledge in one place.
- Danger: becomes a **god object** if every new use case is added to it. Split into multiple facades.
- Real-world: service layer methods like `OrderService.placeOrder()`, SLF4J (also an adapter), `javax.faces.context.FacesContext`.

## Pattern Profile
- **Participants:**
  - `Facade` (`OrderFacade`) — knows which subsystem classes to call and in what order.
  - `Subsystem classes` (`Inventory`, `Payment`, `Shipping`) — do the real work, unaware of the facade.
  - `Client` — calls the facade.
- **Real-world use:** "Place order" orchestrating inventory, payment, shipping; a home theater "watch movie" button; compiler front-ends.
- **Before:** Every client (web, mobile, batch job) repeats the 3-step order sequence and its error handling.
- **After:** One `placeOrder()` call; ordering logic lives in one place.
- **Trade-off:** Can become a bloated god class coupled to everything.
- **vs. [Adapter](Adapter.md):** Facade **simplifies many** classes into a new interface. Adapter **converts one** interface into an expected one. Also vs [Mediator](../Behavioral/Mediator.md): a facade's subsystem doesn't know the facade exists; colleagues in Mediator talk *through* the mediator.

## Java Example
```java
class Inventory {                                       // Subsystem
    boolean reserve(String item) { System.out.println("Reserved " + item); return true; }
}

class Payment {                                         // Subsystem
    boolean charge(String user, double amt) { System.out.println("Charged " + user + " " + amt); return true; }
}

class Shipping {                                        // Subsystem
    void ship(String item, String user) { System.out.println("Shipped " + item + " to " + user); }
}

class OrderFacade {                                     // Facade: one simple entry point
    private final Inventory inventory = new Inventory();
    private final Payment payment = new Payment();
    private final Shipping shipping = new Shipping();

    boolean placeOrder(String user, String item, double price) {
        if (!inventory.reserve(item)) return false;       // ordering knowledge lives here
        if (!payment.charge(user, price)) return false;
        shipping.ship(item, user);
        return true;
    }
}

class Main {
    public static void main(String[] args) {
        new OrderFacade().placeOrder("asha", "Keyboard", 1999); // client sees one call
    }
}
```

> In production, inject the subsystem objects via the constructor ([DIP](../../SOLID/Dependency-Inversion-Principle.md)). They're created inline here for brevity.

## Interview Questions
**Q1. Facade vs Adapter?** *(most common)*
Facade simplifies a whole subsystem behind a new interface. Adapter makes one existing interface match another expected interface.

**Q2. Does a Facade prevent direct access to the subsystem?**
No. It offers a convenient path; clients may still use subsystem classes for advanced needs.

**Q3. Facade vs Mediator?**
Facade is one-directional: client → facade → subsystem, and the subsystem doesn't know the facade. Mediator coordinates two-way communication among peers that know the mediator.

**Q4. How do you prevent a Facade from becoming a god class?**
Keep it thin (orchestration only), split by use case (e.g., `OrderFacade`, `RefundFacade`), and keep logic in the subsystem.

**Q5. Is a service layer a facade?**
Often, yes. It orchestrates repositories, external clients, and domain objects behind use-case methods.

## Common Pitfalls
- Putting business logic into the facade instead of the subsystem.
- Creating a facade that just mirrors one class's methods.
- Confusing it with Adapter.

## Related Topics
- [Adapter](Adapter.md)
- [Mediator](../Behavioral/Mediator.md)
- [Single Responsibility Principle](../../SOLID/Single-Responsibility-Principle.md)
- [Coupling and Cohesion](../../Core-Concepts/Coupling-and-Cohesion.md)

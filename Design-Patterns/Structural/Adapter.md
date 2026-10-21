# Adapter

> **Category:** Structural
> **Intent:** Convert the interface of a class into another interface clients expect, so incompatible classes can work together.

## Quick Summary
- **Definition:** A wrapper that implements the interface your code expects and translates calls to an existing class with a different interface.
- **Problem it solves:** Integrating a third-party/legacy class whose API doesn't match yours, without modifying either side.
- **Use when:** You can't (or shouldn't) change the existing class, but need it to fit an interface.
- **Avoid when:** You control both sides. Just align the interfaces.

## Key Takeaways
- **Object adapter** (composition, wraps an instance) is preferred in Java. **Class adapter** (inheritance) needs multiple inheritance, so it's limited in Java.
- Adapter changes the **interface**, not the behavior.
- Keeps third-party types out of your domain. A common "anti-corruption layer" technique.
- Supports [OCP](../../SOLID/Open-Closed-Principle.md): add a new provider by adding an adapter.
- JDK examples: `Arrays.asList()`, `InputStreamReader` (byte stream → char reader).

## Pattern Profile
- **Participants:**
  - `Target` (`PaymentGateway`) — the interface the client expects.
  - `Adaptee` (`StripeApi`) — existing class with an incompatible interface.
  - `Adapter` (`StripeAdapter`) — implements Target, delegates to Adaptee.
  - `Client` (`CheckoutService`) — works only with Target.
- **Real-world use:** Wrapping payment SDKs, legacy XML services behind a JSON interface, a power plug adapter.
- **Before:** Client code calls `stripe.makeCharge(cents, "INR")` directly, so switching providers means rewriting the client.
- **After:** Client calls `gateway.pay(amount)`. Each provider has its own adapter.
- **Trade-off:** An extra layer of indirection per adaptee; a leaky translation can hide mismatched semantics (e.g., error codes, units).
- **vs. [Facade](Facade.md):** Adapter makes **one** existing interface match an **expected** one. Facade defines a **new, simpler** interface over a **whole subsystem**.

## Java Example
```java
interface PaymentGateway {                         // Target: what our code expects
    void pay(double amountInRupees);
}

class StripeApi {                                  // Adaptee: third-party, can't modify
    void makeCharge(long amountInPaise, String currency) {
        System.out.println("Stripe charged " + amountInPaise + " " + currency);
    }
}

class StripeAdapter implements PaymentGateway {    // Adapter: translates the call
    private final StripeApi stripe;                // composition (object adapter)

    StripeAdapter(StripeApi stripe) { this.stripe = stripe; }

    public void pay(double amountInRupees) {
        long paise = Math.round(amountInRupees * 100);  // translate units
        stripe.makeCharge(paise, "INR");                // translate method + params
    }
}

class CheckoutService {                            // Client: knows only the Target
    private final PaymentGateway gateway;
    CheckoutService(PaymentGateway gateway) { this.gateway = gateway; }
    void checkout(double total) { gateway.pay(total); }
}

class Main {
    public static void main(String[] args) {
        new CheckoutService(new StripeAdapter(new StripeApi())).checkout(499.99);
    }
}
```

## Interview Questions
**Q1. Adapter vs Facade?** *(most common)*
Adapter converts an existing interface into one a client expects (usually one class). Facade simplifies a complex subsystem behind a new interface (usually many classes).

**Q2. Adapter vs Decorator?**
Adapter changes the interface, keeping behavior. Decorator keeps the same interface and adds behavior.

**Q3. Object adapter vs class adapter?**
Object adapter wraps an instance (composition) and works with subclasses of the adaptee. Class adapter extends the adaptee and implements the target. Java's single inheritance makes it rare.

**Q4. Adapter vs Proxy?**
Proxy has the **same** interface as its subject and controls access. Adapter exposes a **different** interface.

**Q5. Where have you seen Adapter in Java?**
`InputStreamReader`, `Arrays.asList()`, Spring MVC `HandlerAdapter`, SLF4J bindings.

**Q6. Can an adapter add behavior?**
It can do translation logic (unit conversion, exception mapping), but adding new features turns it into something else.

## Common Pitfalls
- Confusing Adapter with Facade or Decorator.
- Leaking adaptee types (e.g., `StripeException`) through the target interface.
- Putting business logic in the adapter instead of pure translation.

## Related Topics
- [Facade](Facade.md)
- [Decorator](Decorator.md)
- [Proxy](Proxy.md)
- [Bridge](Bridge.md) — designed up front; Adapter is retrofitted

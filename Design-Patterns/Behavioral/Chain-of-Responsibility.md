# Chain of Responsibility

> **Category:** Behavioral
> **Intent:** Pass a request along a chain of handlers; each handler either processes it or forwards it to the next.

## Quick Summary
- **Definition:** Handlers share an interface and hold a reference to the next handler. The sender doesn't know which handler will process the request.
- **Problem it solves:** A sender with a big `if/else` deciding who handles what, coupled to all possible handlers.
- **Use when:** Multiple objects may handle a request and the handler is determined at runtime; processing pipelines (auth → validate → log).
- **Avoid when:** Every request must be handled and there's a single obvious handler; or debugging an implicit chain would be too costly.

## Key Takeaways
- Two flavors: **"first match handles and stops"** (escalation, e.g., support tiers) vs **"every handler processes and passes on"** (filters/middleware pipeline).
- The sender is decoupled from receivers; chain order/composition can change at runtime.
- **No guarantee of handling.** Add a default/terminal handler.
- Real-world: Servlet `FilterChain`, Spring Security filter chain, logging levels, exception handling in `try/catch` blocks up the call stack.
- Structurally close to [Decorator](../Structural/Decorator.md) (both link objects), but a CoR handler may **stop** propagation.

## Pattern Profile
- **Participants:**
  - `Handler` (`SupportHandler`) — declares `handle()` and holds `next`.
  - `ConcreteHandler` (`Bot`, `Agent`, `Manager`) — handles if it can, otherwise forwards.
  - `Client` — builds the chain and sends requests to the first handler.
- **Real-world use:** Customer support escalation, expense approvals (manager → director → CFO), HTTP middleware, ATM cash dispensing (₹2000 → ₹500 → ₹100 notes).
- **Before:** `if (amount < 1000) manager.approve() else if (amount < 10000) director.approve() ...` inside the client.
- **After:** The client calls `chain.handle(request)`. Approval levels can be added or reordered without touching it.
- **Trade-off:** Requests can go unhandled silently; long chains hurt performance and debuggability.
- **vs. [Decorator](../Structural/Decorator.md):** Decorators always delegate and add behavior. CoR handlers can **short-circuit** and decide whether to pass the request on.

## Java Example
```java
abstract class SupportHandler {                             // Handler
    private SupportHandler next;

    SupportHandler setNext(SupportHandler next) { this.next = next; return next; } // fluent chaining

    void handle(int severity) {
        if (canHandle(severity)) {
            process(severity);
        } else if (next != null) {
            next.handle(severity);                          // forward along the chain
        } else {
            System.out.println("Unhandled severity " + severity); // end of chain
        }
    }

    protected abstract boolean canHandle(int severity);
    protected abstract void process(int severity);
}

class Bot extends SupportHandler {                          // ConcreteHandler
    protected boolean canHandle(int s) { return s <= 1; }
    protected void process(int s) { System.out.println("Bot resolved severity " + s); }
}

class Agent extends SupportHandler {                        // ConcreteHandler
    protected boolean canHandle(int s) { return s <= 3; }
    protected void process(int s) { System.out.println("Agent resolved severity " + s); }
}

class Manager extends SupportHandler {                      // ConcreteHandler
    protected boolean canHandle(int s) { return s <= 5; }
    protected void process(int s) { System.out.println("Manager resolved severity " + s); }
}

class Main {
    public static void main(String[] args) {
        SupportHandler chain = new Bot();
        chain.setNext(new Agent()).setNext(new Manager());  // Bot → Agent → Manager

        chain.handle(1);   // Bot
        chain.handle(3);   // Agent
        chain.handle(5);   // Manager
        chain.handle(9);   // Unhandled
    }
}
```

## Interview Questions
**Q1. Where is Chain of Responsibility used in Java frameworks?** *(most common)*
Servlet `Filter`/`FilterChain`, Spring Security's filter chain, Spring `HandlerInterceptor`s, and logging framework appenders/levels.

**Q2. CoR vs Decorator?**
Similar linked structure. Decorator always calls the wrapped object and adds behavior. CoR handlers decide whether to handle, pass on, or stop.

**Q3. What if no handler processes the request?**
It may be silently dropped. Add a terminal default handler or throw/log at the end of the chain.

**Q4. Who builds the chain?**
The client or configuration (DI container, filter registration). Handlers only know their `next`.

**Q5. How would you design an ATM dispenser with CoR?**
Handlers for ₹2000, ₹500, ₹100. Each dispenses `amount / denomination` notes and forwards the remainder to the next.

**Q6. CoR vs Command?**
Command encapsulates a request as an object. CoR decides who handles a request. They combine well: commands passed down a chain.

## Common Pitfalls
- No terminal handler, so requests vanish silently.
- Handlers that depend on a specific chain order without documenting it.
- Accidentally creating cycles in the chain.

## Related Topics
- [Decorator](../Structural/Decorator.md)
- [Command](Command.md)
- [Composite](../Structural/Composite.md) — a parent can act as the "next" handler
- [Observer](Observer.md) — broadcasts to all; CoR typically stops at one

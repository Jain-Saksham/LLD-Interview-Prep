# Observer

> **Category:** Behavioral
> **Intent:** Define a one-to-many dependency so that when one object changes state, all its dependents are notified automatically.

## Quick Summary
- **Definition:** A subject keeps a list of observers and calls them when an event occurs. Observers subscribe/unsubscribe at runtime.
- **Problem it solves:** A subject hard-coding calls to every interested party (`emailService.send(); smsService.send(); analytics.track();`).
- **Use when:** Changes in one object must trigger updates in an open-ended set of others: event systems, UI updates, pub/sub.
- **Avoid when:** Order of notification matters strictly, or there's one fixed listener. Direct calls are clearer.

## Key Takeaways
- Subject depends only on the `Observer` **interface**: loose coupling, [OCP](../../SOLID/Open-Closed-Principle.md).
- **Push** model (subject sends data in `update(data)`) vs **pull** model (observer queries the subject).
- Pitfalls: **memory leaks** (forgotten unsubscribes; "lapsed listener"), unexpected update order, cascading updates, exceptions in one observer stopping others.
- Synchronous by default. Slow observers block the subject. Async variants use queues/executors.
- `java.util.Observable` is **deprecated** (Java 9). Use your own interface, `PropertyChangeListener`, or reactive libraries.
- Basis of event listeners (Swing), Spring `ApplicationEvent`, Kafka/pub-sub at the system level.

## Pattern Profile
- **Participants:**
  - `Subject` (`StockTicker`) — maintains observers, notifies them.
  - `Observer` (`PriceListener`) — `update()` interface.
  - `ConcreteObserver` (`MobileApp`, `TradingBot`) — reacts to updates.
- **Real-world use:** Stock price alerts, YouTube subscriptions, UI button listeners, order-placed events triggering email/SMS/inventory.
- **Before:** `StockTicker` directly calls `mobileApp.show()` and `bot.trade()`. Adding a dashboard means editing the ticker.
- **After:** New consumers just subscribe; the ticker is unchanged.
- **Trade-off:** Control flow becomes implicit and harder to trace and debug; risk of leaks and notification storms.
- **vs. Pub/Sub:** In Observer, the subject knows its observers directly (in-process). Pub/Sub adds a **broker/event bus** so publishers and subscribers don't know each other, often async/distributed. Also vs [Mediator](Mediator.md): Mediator centralizes many-to-many coordination logic; Observer just broadcasts.

## Java Example
```java
import java.util.ArrayList;
import java.util.List;

interface PriceListener {                                   // Observer
    void onPriceChange(String symbol, double price);
}

class StockTicker {                                         // Subject
    private final List<PriceListener> listeners = new ArrayList<>();

    void subscribe(PriceListener l)   { listeners.add(l); }
    void unsubscribe(PriceListener l) { listeners.remove(l); }  // avoid leaks

    void setPrice(String symbol, double price) {
        for (PriceListener l : listeners) l.onPriceChange(symbol, price); // push model
    }
}

class MobileApp implements PriceListener {                  // ConcreteObserver
    public void onPriceChange(String s, double p) { System.out.println("App: " + s + " = " + p); }
}

class TradingBot implements PriceListener {                 // ConcreteObserver
    public void onPriceChange(String s, double p) {
        if (p < 100) System.out.println("Bot: BUY " + s);
    }
}

class Main {
    public static void main(String[] args) {
        StockTicker ticker = new StockTicker();
        ticker.subscribe(new MobileApp());
        ticker.subscribe(new TradingBot());
        ticker.subscribe((s, p) -> System.out.println("Log: " + s));  // lambda observer

        ticker.setPrice("INFY", 95.5);
    }
}
```

## Interview Questions
**Q1. Observer vs Pub/Sub?** *(most common)*
Observer: the subject holds direct references to observers, usually synchronous and in-process. Pub/Sub: a broker decouples publishers from subscribers, often async and distributed (Kafka, Redis).

**Q2. Push vs pull model?**
Push sends the changed data in the notification (simple, but may send unneeded data). Pull sends only a signal, and observers query the subject (flexible, but more coupling to the subject's API).

**Q3. How can Observer cause memory leaks?**
The subject holds strong references to observers. If observers never unsubscribe, they can't be garbage-collected. Fix with explicit unsubscribe, weak references, or scoped lifecycles.

**Q4. How do you make notification thread-safe?**
Use `CopyOnWriteArrayList` for listeners (safe iteration during subscribe/unsubscribe), or synchronize and iterate over a snapshot.

**Q5. What if one observer throws an exception?**
In a naive loop, the remaining observers aren't notified. Wrap each call in try/catch or dispatch asynchronously.

**Q6. Why was `java.util.Observable` deprecated?**
It's a class (forcing inheritance), not serializable, not thread-safe, and its event model was too limited.

**Q7. How would you notify observers asynchronously?**
Submit each notification to an `ExecutorService` or put events on a queue. Note that ordering guarantees are lost.

## Common Pitfalls
- Forgetting unsubscribe (the lapsed listener leak).
- Modifying the listener list while iterating (`ConcurrentModificationException`).
- Equating Observer with Pub/Sub without mentioning the broker.

## Related Topics
- [Mediator](Mediator.md)
- [Chain of Responsibility](Chain-of-Responsibility.md)
- [Command](Command.md)
- [Open/Closed Principle](../../SOLID/Open-Closed-Principle.md)

# Proxy

> **Category:** Structural
> **Intent:** Provide a surrogate or placeholder for another object to control access to it.

## Quick Summary
- **Definition:** An object with the **same interface** as the real subject that intercepts calls to add access control, laziness, caching, or remoting.
- **Problem it solves:** You need to control *when*, *whether*, or *how* a real object is used, without changing it or its clients.
- **Use when:** Lazy initialization of heavy objects, permission checks, caching, logging, remote calls.
- **Avoid when:** The indirection adds latency or complexity without a real access concern.

## Key Takeaways
- Common types: **Virtual** (lazy load), **Protection** (access control), **Remote** (RPC stub), **Caching**, **Logging/Smart reference**.
- Client is typically **unaware** it's talking to a proxy.
- The proxy often **creates/manages** the real subject (unlike Decorator, which is handed one).
- Java support: `java.lang.reflect.Proxy` (dynamic proxies for interfaces); Spring AOP (`@Transactional`, `@Cacheable`) and Hibernate lazy loading use proxies.
- Classic Spring gotcha: **self-invocation** bypasses the proxy, so `this.method()` skips `@Transactional`.

## Pattern Profile
- **Participants:**
  - `Subject` (`Image`) — common interface.
  - `RealSubject` (`HighResImage`) — the expensive/protected object.
  - `Proxy` (`LazyImageProxy`) — controls access to the RealSubject.
- **Real-world use:** Hibernate lazy entities, Spring `@Transactional`, gRPC/RMI stubs, CDN/cache in front of a service, credit card as a proxy for a bank account.
- **Before:** All images load from disk at startup even if never displayed.
- **After:** Images load on first `display()` call; clients unchanged.
- **Trade-off:** Added latency/indirection; first access can be slow (lazy load), and behavior can surprise (e.g., `LazyInitializationException`).
- **vs. [Decorator](Decorator.md):** Same structure. Proxy **controls access** to the same behavior; Decorator **adds new behavior** and is often stacked by the client.

## Java Example
```java
interface Image {                                    // Subject
    void display();
}

class HighResImage implements Image {                // RealSubject: expensive to create
    private final String file;
    HighResImage(String file) {
        this.file = file;
        System.out.println("Loading " + file + " from disk...");   // costly
    }
    public void display() { System.out.println("Displaying " + file); }
}

class LazyImageProxy implements Image {              // Proxy: same interface
    private final String file;
    private HighResImage real;                       // created only when needed

    LazyImageProxy(String file) { this.file = file; }

    public void display() {
        if (real == null) real = new HighResImage(file);  // virtual proxy: lazy init
        real.display();                                    // delegate
    }
}

class Main {
    public static void main(String[] args) {
        Image img = new LazyImageProxy("photo.png");  // nothing loaded yet
        System.out.println("Gallery opened");
        img.display();                                // loads now, then displays
        img.display();                                // already loaded
    }
}
```

## Interview Questions
**Q1. Proxy vs Decorator?** *(most common)*
Structurally identical. Intent differs: Proxy controls access (laziness, security, remoting) and usually manages its subject; Decorator adds responsibilities and is composed/stacked by the client.

**Q2. What types of proxies exist?**
Virtual (lazy), protection (authorization), remote (network stub), caching, and logging/smart-reference proxies.

**Q3. How does Spring use proxies?**
Beans with `@Transactional`, `@Async`, `@Cacheable` are wrapped in JDK dynamic proxies (interfaces) or CGLIB subclasses, which add behavior around method calls.

**Q4. Why doesn't `@Transactional` work on a method called from the same class?**
`this.method()` calls the real object directly, bypassing the proxy that starts the transaction.

**Q5. JDK dynamic proxy vs CGLIB?**
JDK proxies work only with interfaces. CGLIB subclasses the concrete class, so it can't proxy `final` classes/methods.

**Q6. Proxy vs Adapter?**
Proxy keeps the same interface. Adapter provides a different one.

## Common Pitfalls
- Saying Proxy "adds features". That's Decorator's intent.
- Forgetting thread safety in lazy-init proxies.
- Not knowing the self-invocation limitation of Spring proxies.

## Related Topics
- [Decorator](Decorator.md)
- [Adapter](Adapter.md)
- [Facade](Facade.md)
- [Singleton](../Creational/Singleton.md) — lazy-init concerns are similar

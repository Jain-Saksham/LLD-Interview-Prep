# Bridge

> **Category:** Structural
> **Intent:** Decouple an abstraction from its implementation so the two can vary independently.

## Quick Summary
- **Definition:** Split one class hierarchy into two: an **abstraction** hierarchy that holds a reference to an **implementation** hierarchy.
- **Problem it solves:** **Cartesian-product class explosion** when two dimensions vary (`CircleRed`, `CircleBlue`, `SquareRed`, ...).
- **Use when:** A class varies along two or more independent dimensions, or you need to switch implementations at runtime.
- **Avoid when:** There's only one dimension of variation. It's added complexity for nothing.

## Key Takeaways
- Turns **M × N** subclasses into **M + N** classes.
- "Implementation" here means a separate class hierarchy (platform, device, renderer), not "implementation" in the `implements` sense.
- Applied [composition over inheritance](../../Core-Concepts/Composition-vs-Inheritance.md) at the architecture level.
- Designed **up front**, unlike [Adapter](Adapter.md), which is retrofitted.
- Real-world: JDBC (`DriverManager`/`Connection` API ↔ vendor drivers), SLF4J API ↔ Logback/Log4j.

## Pattern Profile
- **Participants:**
  - `Abstraction` (`Notification`) — high-level control; holds an `Implementor`.
  - `RefinedAbstraction` (`AlertNotification`, `PromoNotification`) — variants of the abstraction.
  - `Implementor` (`Sender`) — low-level operations interface.
  - `ConcreteImplementor` (`EmailSender`, `SmsSender`) — platform-specific implementations.
- **Real-world use:** Notification type × delivery channel, remote control × device, shape × renderer, JDBC.
- **Before:** `AlertEmail`, `AlertSms`, `PromoEmail`, `PromoSms`... a new channel adds one class per notification type.
- **After:** 2 notification types + 2 senders, combined at runtime.
- **Trade-off:** More upfront design and indirection; overkill if the second dimension never materializes.
- **vs. [Strategy](../Behavioral/Strategy.md):** Structurally similar (composition + interface). Bridge is a **structural** split of a whole abstraction into two hierarchies that evolve independently. Strategy swaps **one algorithm** behind a stable context.

## Java Example
```java
interface Sender {                                    // Implementor
    void send(String to, String text);
}

class EmailSender implements Sender {                 // ConcreteImplementor
    public void send(String to, String text) { System.out.println("Email to " + to + ": " + text); }
}

class SmsSender implements Sender {                   // ConcreteImplementor
    public void send(String to, String text) { System.out.println("SMS to " + to + ": " + text); }
}

abstract class Notification {                         // Abstraction
    protected final Sender sender;                    // the "bridge"
    Notification(Sender sender) { this.sender = sender; }
    abstract void notifyUser(String user);
}

class AlertNotification extends Notification {        // RefinedAbstraction
    AlertNotification(Sender s) { super(s); }
    void notifyUser(String user) { sender.send(user, "ALERT: suspicious login"); }
}

class PromoNotification extends Notification {        // RefinedAbstraction
    PromoNotification(Sender s) { super(s); }
    void notifyUser(String user) { sender.send(user, "50% off today!"); }
}

class Main {
    public static void main(String[] args) {
        new AlertNotification(new SmsSender()).notifyUser("asha");    // any type × any channel
        new PromoNotification(new EmailSender()).notifyUser("ravi");
    }
}
```

## Interview Questions
**Q1. What problem does Bridge solve?** *(most common)*
Class explosion when two dimensions vary independently. It replaces M×N subclasses with M+N classes connected by composition.

**Q2. Bridge vs Adapter?**
Bridge is designed up front so abstraction and implementation can evolve separately. Adapter is applied after the fact to make incompatible existing interfaces work together.

**Q3. Bridge vs Strategy?**
Similar structure. Bridge separates a whole abstraction from its platform/implementation hierarchy (structural). Strategy encapsulates interchangeable algorithms for one task (behavioral).

**Q4. Give a real-world Java example.**
JDBC: your code uses the `Connection`/`Statement` abstraction, and vendor drivers provide the implementation.

**Q5. Can the implementation be changed at runtime?**
Yes, if the abstraction exposes a setter or is re-created with a different implementor.

## Common Pitfalls
- Applying Bridge when only one dimension varies.
- Confusing "implementation" with the `implements` keyword.
- Letting the abstraction leak implementor-specific details.

## Related Topics
- [Adapter](Adapter.md)
- [Strategy](../Behavioral/Strategy.md)
- [Abstract Factory](../Creational/Abstract-Factory.md) — can create a matching abstraction/implementor pair
- [Composition vs Inheritance](../../Core-Concepts/Composition-vs-Inheritance.md)

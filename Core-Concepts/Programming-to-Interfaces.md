# Programming to Interfaces

## Quick Summary
- **Definition:** Write code against an abstract type (interface/supertype) describing **what** an object does, not against a concrete class describing **how**. ("Program to an interface, not an implementation." GoF.)
- **Problem it solves:** Code that breaks or needs editing whenever the concrete implementation changes.
- **Use when:** Declaring fields, parameters, and return types for collaborators that may vary or need mocking.
- **Avoid over-applying when:** Creating interfaces for classes that will only ever have one implementation and aren't boundaries.

## Key Takeaways
- "Interface" here means **supertype** (interface or abstract class), not only the Java keyword.
- Everyday example: `List<String> names = new ArrayList<>();` instead of `ArrayList<String> names = ...`.
- Enables **polymorphism**, [OCP](../SOLID/Open-Closed-Principle.md), [DIP](../SOLID/Dependency-Inversion-Principle.md), and easy mocking in tests.
- Declare the **most general type** that provides what the code needs (`Collection` vs `List` vs `ArrayList`). Return types can be slightly more specific if callers need it.
- The concrete type should appear in **only one place**: where the object is created (factory, DI config, `main`).

## Java Example
```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;

interface MessageSender {                         // what, not how
    void send(String to, String msg);
}

class EmailSender implements MessageSender {
    public void send(String to, String msg) { System.out.println("Email " + to + ": " + msg); }
}

class SlackSender implements MessageSender {
    public void send(String to, String msg) { System.out.println("Slack " + to + ": " + msg); }
}

class AlertService {
    private final MessageSender sender;           // field typed to the interface
    AlertService(MessageSender sender) { this.sender = sender; }

    void alertAll(List<String> users) {           // parameter typed to List, not ArrayList
        for (String u : users) sender.send(u, "Server down!");
    }
}

class Main {
    public static void main(String[] args) {
        List<String> users = new ArrayList<>(List.of("asha", "ravi")); // can swap to LinkedList freely
        new AlertService(new EmailSender()).alertAll(users);
        new AlertService(new SlackSender()).alertAll(new LinkedList<>(users));
    }
}
```

## Interview Questions
**Q1. Why write `List<String> list = new ArrayList<>()` instead of `ArrayList<String> list = ...`?** *(most common)*
The rest of the code depends only on `List`, so you can switch to `LinkedList` or an immutable list by changing one line, and methods accepting `List` work with any implementation.

**Q2. Does "interface" in this principle mean only the `interface` keyword?**
No. It means any supertype: an interface or abstract class that defines the contract.

**Q3. How does this principle help testing?**
Collaborators typed as interfaces can be replaced with mocks or fakes without changing the class under test.

**Q4. When should you NOT introduce an interface?**
For stable internal classes with a single implementation and no testing or swapping need. Interfaces there add indirection only.

**Q5. How does it relate to DIP and DI?**
Programming to interfaces is the habit; DIP is the architectural principle built on it; DI supplies the concrete implementation at runtime.

## Common Pitfalls
- Declaring parameters as concrete types (`ArrayList`, `HashMap`) unnecessarily.
- Creating `IService`/`ServiceImpl` pairs reflexively for everything.
- Casting the interface back to a concrete type (`(ArrayList) list`), which defeats the point.

## Related Topics
- [Dependency Inversion Principle](../SOLID/Dependency-Inversion-Principle.md)
- [Dependency Injection](Dependency-Injection.md)
- [Class vs Interface vs Abstract Class](Class-vs-Interface-vs-Abstract-Class.md)
- [Strategy](../Design-Patterns/Behavioral/Strategy.md)

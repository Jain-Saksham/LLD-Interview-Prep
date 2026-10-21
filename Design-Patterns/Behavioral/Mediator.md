# Mediator

> **Category:** Behavioral
> **Intent:** Define an object that encapsulates how a set of objects interact, so they don't refer to each other directly.

## Quick Summary
- **Definition:** Colleagues send messages to a central mediator, which routes them or coordinates the response.
- **Problem it solves:** A tangled web of N×N references between peer objects; every change ripples everywhere.
- **Use when:** Many objects communicate in complex but well-defined ways (chat rooms, UI form widgets, air traffic control).
- **Avoid when:** Only two or three objects interact simply. The mediator adds an unnecessary hub.

## Key Takeaways
- Converts **many-to-many** relationships into **one-to-many** (each colleague ↔ mediator).
- Colleagues know only the mediator interface, so they're reusable and loosely [coupled](../../Core-Concepts/Coupling-and-Cohesion.md).
- Interaction logic is centralized in **one place**, which is easier to change and understand.
- Risk: the mediator becomes a **god object** containing all the logic.
- Real-world: chat servers, air traffic control tower, MVC controllers, `java.util.Timer` (schedules tasks), message brokers at the system level.

## Pattern Profile
- **Participants:**
  - `Mediator` (`ChatMediator`) — interface for communication.
  - `ConcreteMediator` (`ChatRoom`) — coordinates colleagues.
  - `Colleague` (`User`) — communicates only via the mediator.
- **Real-world use:** Chat rooms, flight control towers, UI dialogs where checking one box enables another field, auction systems.
- **Before:** Each `User` holds references to every other user to send messages (N² links).
- **After:** Users talk to `ChatRoom`, which broadcasts. Adding a user doesn't affect others.
- **Trade-off:** The mediator can grow into a complex, hard-to-maintain god class.
- **vs. [Observer](Observer.md):** Observer is **one-way broadcast** from a subject to passive listeners. Mediator **coordinates two-way interactions** between peers and contains the interaction logic. (A mediator is often *implemented* using Observer.) Also see [Facade](../Structural/Facade.md).

## Java Example
```java
import java.util.ArrayList;
import java.util.List;

interface ChatMediator {                                   // Mediator
    void join(User user);
    void send(String msg, User from);
}

class ChatRoom implements ChatMediator {                   // ConcreteMediator
    private final List<User> users = new ArrayList<>();

    public void join(User user) { users.add(user); }

    public void send(String msg, User from) {
        for (User u : users)
            if (u != from) u.receive(msg, from.name);      // routing logic lives here
    }
}

class User {                                               // Colleague
    final String name;
    private final ChatMediator room;                       // knows only the mediator

    User(String name, ChatMediator room) { this.name = name; this.room = room; room.join(this); }

    void send(String msg) { room.send(msg, this); }
    void receive(String msg, String from) { System.out.println(name + " got '" + msg + "' from " + from); }
}

class Main {
    public static void main(String[] args) {
        ChatMediator room = new ChatRoom();
        User asha = new User("Asha", room);
        new User("Ravi", room);
        new User("Meera", room);

        asha.send("Hi all!");   // Ravi and Meera receive it; Asha doesn't
    }
}
```

## Interview Questions
**Q1. Mediator vs Observer?** *(most common)*
Observer: a subject broadcasts changes to dependents, one-way and without coordination logic. Mediator: peers communicate through a hub that holds the interaction rules, two-way and coordinated.

**Q2. Mediator vs Facade?**
Facade simplifies a subsystem for external clients, and the subsystem is unaware of it. Mediator sits among peers that actively talk through it.

**Q3. What's the main risk?**
The mediator becomes a god object. Mitigate by splitting mediators per interaction area.

**Q4. Real-world example?**
Air traffic control: planes don't talk to each other; they coordinate via the tower.

**Q5. How does Mediator help with coupling?**
Colleagues depend on one mediator interface instead of each other, so you can reuse or replace them independently.

## Common Pitfalls
- Letting all business logic migrate into the mediator.
- Using Mediator when objects barely interact.
- Confusing it with Observer or Facade.

## Related Topics
- [Observer](Observer.md)
- [Facade](../Structural/Facade.md)
- [Coupling and Cohesion](../../Core-Concepts/Coupling-and-Cohesion.md)

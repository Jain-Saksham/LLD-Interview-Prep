# State

> **Category:** Behavioral
> **Intent:** Allow an object to alter its behavior when its internal state changes, as if it changed its class.

## Quick Summary
- **Definition:** Each state is a class implementing a common interface. The context delegates to its current state object, and states switch the context to the next state.
- **Problem it solves:** Methods riddled with `switch (state)` blocks that must all be updated when a new state is added.
- **Use when:** An object has a clear finite set of states with different behavior per state and well-defined transitions (vending machine, order lifecycle, traffic light).
- **Avoid when:** Only 2–3 states with trivial behavior. An `enum` + `switch` is simpler.

## Key Takeaways
- Models a **finite state machine** with polymorphism instead of conditionals.
- Each state class encapsulates **behavior + allowed transitions** for that state.
- Transitions can live in states (decentralized) or the context (centralized). States-driven is more common.
- Invalid actions in a state are handled explicitly (e.g., "can't dispense without coin").
- Java `enum`s with abstract methods are a compact alternative for simple machines.

## Pattern Profile
- **Participants:**
  - `Context` (`VendingMachine`) — holds the current state, delegates requests.
  - `State` (`MachineState`) — interface for state-specific behavior.
  - `ConcreteState` (`IdleState`, `HasCoinState`) — behavior for one state; triggers transitions.
- **Real-world use:** Vending/ATM machines, order lifecycle (Placed → Shipped → Delivered), TCP connection, document workflow (Draft → Review → Published), media player.
- **Before:** `insertCoin()`, `pressButton()`, `refund()` each contain `if (state == IDLE) ... else if (state == HAS_COIN) ...`.
- **After:** Each state class handles all actions for that state. Adding a state = one new class.
- **Trade-off:** Many small classes; transition logic is spread across state classes, which makes the full state machine harder to see at a glance.
- **vs. [Strategy](Strategy.md):** Same structure. State objects **know about and switch to each other** based on internal events. Strategies are **independent** and chosen by the client.

## Java Example
```java
interface MachineState {                                   // State
    void insertCoin(VendingMachine m);
    void pressButton(VendingMachine m);
}

class IdleState implements MachineState {                  // ConcreteState
    public void insertCoin(VendingMachine m) {
        System.out.println("Coin accepted");
        m.setState(new HasCoinState());                    // state triggers transition
    }
    public void pressButton(VendingMachine m) { System.out.println("Insert coin first"); }
}

class HasCoinState implements MachineState {               // ConcreteState
    public void insertCoin(VendingMachine m) { System.out.println("Coin already inserted"); }
    public void pressButton(VendingMachine m) {
        System.out.println("Dispensing item");
        m.setState(new IdleState());
    }
}

class VendingMachine {                                     // Context
    private MachineState state = new IdleState();

    void setState(MachineState state) { this.state = state; }
    void insertCoin()  { state.insertCoin(this); }         // delegate to current state
    void pressButton() { state.pressButton(this); }
}

class Main {
    public static void main(String[] args) {
        VendingMachine vm = new VendingMachine();
        vm.pressButton();   // Insert coin first
        vm.insertCoin();    // Coin accepted
        vm.pressButton();   // Dispensing item
    }
}
```

## Interview Questions
**Q1. State vs Strategy?** *(most common)*
Same structure, different intent. State: the object changes behavior as its internal state changes, and states transition among themselves. Strategy: the client injects an algorithm, and strategies don't know each other.

**Q2. Where should transition logic live?**
In state classes (each knows its successors; easy to add states, harder to see the whole machine) or in the context/a transition table (centralized and visible, but the context grows). Pick based on complexity.

**Q3. When is an `enum` + `switch` better?**
Few states, simple behavior, and transitions unlikely to change. Fewer classes, and the whole machine is visible in one place.

**Q4. How do you handle invalid operations in a state?**
Each state explicitly handles every action: print/log an error, throw `IllegalStateException`, or ignore. Don't silently fall through.

**Q5. Should state objects be shared or new per transition?**
Stateless state objects can be shared singletons (or enum constants) to avoid allocation. Create new ones only if they hold per-context data.

**Q6. Design an order lifecycle using State.**
`OrderState` with `next()`, `cancel()`. `Placed.next()` → `Shipped`; `Shipped.cancel()` → error; `Delivered.next()` → error. `Order` delegates to the current state.

## Common Pitfalls
- Confusing it with Strategy, or being unable to explain who triggers transitions.
- Leaving a `switch` on state somewhere in the context, which defeats the pattern.
- Not handling invalid actions per state.

## Related Topics
- [Strategy](Strategy.md)
- [Flyweight](../Structural/Flyweight.md) / [Singleton](../Creational/Singleton.md) — sharing stateless state objects
- [Open/Closed Principle](../../SOLID/Open-Closed-Principle.md)

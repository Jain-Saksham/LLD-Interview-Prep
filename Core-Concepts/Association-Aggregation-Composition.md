# Association, Aggregation, Composition

## Quick Summary
- **Definition:** Three kinds of object relationships, ordered by increasing strength of ownership.
  - **Association:** objects know/use each other; independent lifecycles ("uses-a").
  - **Aggregation:** a whole contains parts, but parts **can exist independently** ("has-a", weak ownership).
  - **Composition:** a whole **owns** its parts; parts **can't exist without** the whole ("part-of", strong ownership).
- **Problem it solves:** Communicating ownership and lifecycle precisely in designs and UML diagrams.

## Key Takeaways
| | Association | Aggregation | Composition |
|---|---|---|---|
| Meaning | Uses / knows | Has (shared) | Owns (exclusive) |
| Lifecycle | Independent | Part outlives whole | Part dies with whole |
| Sharing | Yes | Part can belong to many wholes | Part belongs to one whole |
| UML | Plain line `—` | Hollow diamond `◇—` at the whole | Filled diamond `◆—` at the whole |
| Example | Doctor ↔ Patient | Team ◇— Player | House ◆— Room |

- The deciding question: **"If the whole is destroyed, does the part make sense on its own?"**
- In Java code, composition usually means the whole **creates** the part internally and never exposes it for sharing; aggregation means the part is **passed in**.
- Java has GC, so "destroy" means "no longer reachable". The distinction is conceptual, not enforced by the language.
- Aggregation and composition are both special forms of association.

## Java Example
```java
import java.util.ArrayList;
import java.util.List;

class Doctor {                                   // Association: uses a Patient temporarily
    void treat(Patient p) { System.out.println("Treating " + p.name); }
}

class Patient {
    final String name;
    Patient(String name) { this.name = name; }
}

class Player {
    final String name;
    Player(String name) { this.name = name; }
}

class Team {                                     // Aggregation: players exist independently
    private final List<Player> players = new ArrayList<>();
    void add(Player p) { players.add(p); }       // created outside, passed in
}

class Room {
    final String type;
    Room(String type) { this.type = type; }
}

class House {                                    // Composition: House owns its Rooms
    private final List<Room> rooms = new ArrayList<>();
    House() {
        rooms.add(new Room("Kitchen"));          // created inside, never shared
        rooms.add(new Room("Bedroom"));
    }
    int roomCount() { return rooms.size(); }
}

class Main {
    public static void main(String[] args) {
        new Doctor().treat(new Patient("Asha"));

        Player virat = new Player("Virat");
        Team india = new Team();
        india.add(virat);                         // virat still exists if india is discarded

        House house = new House();                // rooms live and die with the house
        System.out.println("Rooms: " + house.roomCount());
    }
}
```

## Interview Questions
**Q1. Difference between aggregation and composition?** *(most common)*
Both are "has-a". In aggregation, the part can exist independently and be shared (Team–Player). In composition, the part's lifecycle is bound to the whole and it's exclusively owned (House–Room, Order–OrderLine).

**Q2. How do you show these in UML?**
Association: plain line. Aggregation: hollow diamond at the whole. Composition: filled diamond at the whole.

**Q3. How does composition look in Java code?**
The whole creates the part internally (often a `private final` field), doesn't expose it for external sharing, and the part has no meaning outside it.

**Q4. Give examples from an LLD problem (e.g., Parking Lot).**
ParkingLot ◆— ParkingFloor (composition); ParkingSpot — Vehicle (association, temporary); Library ◇— Book could be aggregation if books can move between libraries.

**Q5. Is "composition" here the same as in "composition over inheritance"?**
Related but broader there. "Composition over inheritance" means building behavior by holding references to other objects (it covers aggregation too). In UML, composition specifically means strong ownership.

## Common Pitfalls
- Swapping the diamond meanings (hollow = aggregation, filled = composition).
- Putting the diamond at the wrong end. It goes on the **whole**, not the part.
- Over-analyzing aggregation vs association. Many interviewers care mainly about composition vs aggregation.

## Related Topics
- [UML Class Diagrams](UML-Class-Diagrams.md)
- [Composition vs Inheritance](Composition-vs-Inheritance.md)
- [Composite](../Design-Patterns/Structural/Composite.md)

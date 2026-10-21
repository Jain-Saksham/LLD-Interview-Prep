# LLD Case Studies

A place for end-to-end design problems. Add one file per problem (e.g., `Parking-Lot.md`) as you practice, and link it in the table below.

## Standard Approach (use for every case study)
1. **Requirements:** 4–6 functional requirements and key constraints. State what's out of scope.
2. **Entities and relationships:** core classes, enums, and a quick [UML sketch](../Core-Concepts/UML-Class-Diagrams.md).
3. **Key interfaces:** points of variation (pricing, payment, notification, allocation).
4. **Patterns used:** name each pattern, *why* it's used, and its trade-off.
5. **Core Java code:** main classes and the primary flow only.
6. **Concurrency and edge cases:** shared state, locking, failure paths.
7. **Extensions:** how the design handles new requirements.

## Common Problems and Patterns They Typically Use
| Problem | Typical patterns / concepts | Status |
|---|---|---|
| Parking Lot | [Strategy](../Design-Patterns/Behavioral/Strategy.md) (spot allocation, pricing), [Factory Method](../Design-Patterns/Creational/Factory-Method.md) (vehicles/spots), [Singleton](../Design-Patterns/Creational/Singleton.md) (lot) | ☐ |
| Elevator System | [State](../Design-Patterns/Behavioral/State.md) (elevator), [Strategy](../Design-Patterns/Behavioral/Strategy.md) (scheduling), [Observer](../Design-Patterns/Behavioral/Observer.md) (displays) | ☐ |
| Vending Machine | [State](../Design-Patterns/Behavioral/State.md) | ☐ |
| Library Management | [Observer](../Design-Patterns/Behavioral/Observer.md) (reservations), [Composition](../Core-Concepts/Association-Aggregation-Composition.md) | ☐ |
| BookMyShow / Movie Booking | Concurrency (seat locking), [Strategy](../Design-Patterns/Behavioral/Strategy.md) (pricing), [Observer](../Design-Patterns/Behavioral/Observer.md) (notifications) | ☐ |
| Splitwise | [Strategy](../Design-Patterns/Behavioral/Strategy.md) (split types), graph simplification | ☐ |
| Snake & Ladder / Tic-Tac-Toe / Chess | [Strategy](../Design-Patterns/Behavioral/Strategy.md) (moves/players), [Factory Method](../Design-Patterns/Creational/Factory-Method.md) (pieces) | ☐ |
| Logger Framework | [Chain of Responsibility](../Design-Patterns/Behavioral/Chain-of-Responsibility.md) (levels), [Singleton](../Design-Patterns/Creational/Singleton.md), [Observer](../Design-Patterns/Behavioral/Observer.md) (appenders) | ☐ |
| Rate Limiter | [Strategy](../Design-Patterns/Behavioral/Strategy.md) (token bucket / sliding window), concurrency | ☐ |
| Food Delivery / Ride Sharing | [Strategy](../Design-Patterns/Behavioral/Strategy.md) (matching), [Observer](../Design-Patterns/Behavioral/Observer.md) (status), [State](../Design-Patterns/Behavioral/State.md) (order/ride) | ☐ |

When you add a case study, change ☐ to a link to its file and add it to the root [README](../README.md) checklist.

## Related
- [Core LLD Questions](../Interview-Questions/Core-LLD-Questions.md) — the step-by-step LLD round approach
- [Design Pattern Questions](../Interview-Questions/Design-Pattern-Questions.md) — scenario → pattern table

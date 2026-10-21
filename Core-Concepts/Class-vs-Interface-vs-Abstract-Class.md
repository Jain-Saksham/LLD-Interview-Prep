# Class vs Interface vs Abstract Class

## Quick Summary
- **Concrete class:** A complete, instantiable implementation with state and behavior.
- **Abstract class:** A partial implementation. It can hold **state, constructors, and concrete methods**, plus abstract methods for subclasses to fill. Can't be instantiated.
- **Interface:** A **contract/capability** with no instance state. Since Java 8 it can have `default`/`static` methods; since Java 9, `private` methods.
- **Problem it solves:** Choosing how to share behavior and define contracts between components.

## Key Takeaways
| Feature | Abstract Class | Interface |
|---|---|---|
| Instance fields (state) | Yes | No (only `public static final` constants) |
| Constructors | Yes | No |
| Method bodies | Yes | `default`, `static`, `private` (Java 8/9+) |
| Access modifiers on methods | Any | Implicitly `public` (except `private` helpers) |
| Inheritance | Single (`extends` one) | Multiple (`implements` many) |
| Relationship | "is-a" (shared identity) | "can-do" (capability) |

- **Default to interfaces** for contracts between components ([Programming to Interfaces](Programming-to-Interfaces.md)); they allow multiple implementations and easy mocking.
- Use an **abstract class** when related classes share **state or common implementation** (e.g., [Template Method](../Design-Patterns/Behavioral/Template-Method.md)).
- Common combo: interface for the contract + abstract "skeletal implementation" (e.g., `List` + `AbstractList`).
- Default methods exist mainly to **evolve interfaces** without breaking implementers, not to replace abstract classes.
- **Diamond conflict:** if two interfaces provide the same default method, the class must override it (`A.super.method()` to choose).

## Java Example
```java
interface Payable {                                      // capability/contract
    double pay();
    default String currency() { return "INR"; }          // default: evolves interface safely
}

abstract class Employee implements Payable {             // shared state + partial behavior
    protected final String name;
    protected final double baseSalary;

    Employee(String name, double baseSalary) {           // abstract classes can have constructors
        this.name = name; this.baseSalary = baseSalary;
    }

    abstract double bonus();                             // subclasses must define

    public double pay() { return baseSalary + bonus(); } // shared implementation
}

class Engineer extends Employee {                        // concrete class
    Engineer(String name) { super(name, 100_000); }
    double bonus() { return 20_000; }
}

class Contractor implements Payable {                    // unrelated class, same capability
    public double pay() { return 80_000; }
}

class Main {
    public static void main(String[] args) {
        Payable[] payees = { new Engineer("Asha"), new Contractor() };
        for (Payable p : payees) System.out.println(p.pay() + " " + p.currency());
    }
}
```

## Interview Questions
**Q1. When would you choose an abstract class over an interface?** *(most common)*
When subclasses share state (fields), constructor logic, or substantial common implementation and are closely related. Choose an interface to define a capability that unrelated classes can implement, or when multiple inheritance of type is needed.

**Q2. After Java 8 default methods, why do we still need abstract classes?**
Interfaces still can't have instance state, constructors, or non-public instance methods (besides private helpers). Abstract classes can.

**Q3. What happens if a class implements two interfaces with the same default method?**
Compilation error unless the class overrides the method. It can delegate via `InterfaceA.super.method()`.

**Q4. Can an abstract class have no abstract methods?**
Yes. It just prevents instantiation (e.g., a base class meant only for extension).

**Q5. Can an interface have a constructor or instance variables?**
No. Fields in interfaces are implicitly `public static final`.

**Q6. What is a skeletal implementation?**
An abstract class implementing an interface's boilerplate (e.g., `AbstractList` for `List`) so implementers only write the essentials. You get interface flexibility plus implementation reuse.

**Q7. What is a marker interface?**
An interface with no methods (e.g., `Serializable`, `Cloneable`) that tags a class for special treatment. Annotations are the modern alternative, though marker interfaces give compile-time type checking.

## Common Pitfalls
- Saying "interfaces can't have method bodies" (outdated since Java 8).
- Using abstract classes just to share a utility method. Composition is usually better.
- Forgetting the diamond-resolution rule for default methods.

## Related Topics
- [OOP Principles](OOP-Principles.md)
- [Programming to Interfaces](Programming-to-Interfaces.md)
- [Interface Segregation Principle](../SOLID/Interface-Segregation-Principle.md)
- [Template Method](../Design-Patterns/Behavioral/Template-Method.md)

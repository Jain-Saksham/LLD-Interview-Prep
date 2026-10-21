# Single Responsibility Principle (SRP)

## Quick Summary
- **Definition:** A class should have **one, and only one, reason to change** — i.e., it should serve one actor/stakeholder.
- **Plain English:** Don't mix business rules, persistence, and formatting in one class; when the DBA, the accountant, and the UI team all edit the same file, it's doing too much.
- **Problem it solves:** Changes for one concern accidentally breaking another; huge classes that are hard to test.
- **Avoid over-applying when:** Splitting would create many anemic one-method classes with no independent reason to change.

## Key Takeaways
- "Responsibility" = **reason to change**, not "does one thing" or "has one method".
- Think in terms of **actors**: who would request a change to this code?
- High [cohesion](../Core-Concepts/Coupling-and-Cohesion.md) is SRP's measurable effect.
- Applies at every level: methods, classes, modules, services.
- Over-splitting is a real cost — more files, more indirection.

## Violation
```java
// Three reasons to change: pay rules (finance), storage (DBA), report format (UI/ops).
class Employee {
    String name;
    double hourlyRate;
    int hours;

    double calculatePay() { return hourlyRate * hours; }               // business rule
    void saveToDatabase() { System.out.println("INSERT " + name); }     // persistence
    String toReport() { return name + ": " + calculatePay(); }          // presentation
}
```

## Corrected Design
```java
class Employee {                       // just data + identity
    final String name;
    final double hourlyRate;
    final int hours;
    Employee(String name, double hourlyRate, int hours) {
        this.name = name; this.hourlyRate = hourlyRate; this.hours = hours;
    }
}

class PayCalculator {                  // changes only when pay rules change
    double pay(Employee e) { return e.hourlyRate * e.hours; }
}

class EmployeeRepository {             // changes only when storage changes
    void save(Employee e) { System.out.println("INSERT " + e.name); }
}

class PayReportFormatter {             // changes only when report format changes
    String format(Employee e, double pay) { return e.name + ": " + pay; }
}

class Main {
    public static void main(String[] args) {
        Employee e = new Employee("Asha", 50, 40);
        double pay = new PayCalculator().pay(e);
        new EmployeeRepository().save(e);
        System.out.println(new PayReportFormatter().format(e, pay)); // Asha: 2000.0
    }
}
```

**Key benefit:** Each change is isolated to one small, independently testable class.

## Interview Questions
**Q1. What does "one reason to change" actually mean?** *(most common)*
One actor or stakeholder who would request a change. A class serving finance and the DBA has two reasons to change.

**Q2. Does SRP mean a class should have only one method?**
No. A `PayCalculator` can have many methods as long as they all serve pay rules.

**Q3. How do you detect an SRP violation?**
Class name contains "And"/"Manager"/"Util", unrelated imports (JDBC + formatting), edits for unrelated features hit the same file, tests need lots of unrelated mocks.

**Q4. What's the downside of applying SRP aggressively?**
Class explosion and scattered logic; you trade one big class for many tiny ones that are harder to navigate.

**Q5. How is SRP related to cohesion?**
SRP produces high cohesion: everything in the class relates to the same purpose.

**Q6. Which patterns help achieve SRP?**
[Facade](../Design-Patterns/Structural/Facade.md) (separate orchestration), [Decorator](../Design-Patterns/Structural/Decorator.md) (separate cross-cutting add-ons), [Command](../Design-Patterns/Behavioral/Command.md) (separate request from executor), Repository (separate persistence).

## Common Pitfalls
- Defining responsibility as "does one thing" instead of "one reason to change".
- Splitting by technical layer only, while still mixing unrelated business rules in one class.
- Creating classes so small that a single feature requires touching ten of them.

## Related Topics
- [Coupling and Cohesion](../Core-Concepts/Coupling-and-Cohesion.md)
- [Interface Segregation Principle](Interface-Segregation-Principle.md) — SRP for interfaces
- [Facade](../Design-Patterns/Structural/Facade.md)

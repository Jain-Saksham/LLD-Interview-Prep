# Interface Segregation Principle (ISP)

## Quick Summary
- **Definition:** Clients should not be forced to depend on methods they do not use.
- **Plain English:** Prefer several small, role-specific interfaces over one "fat" interface.
- **Problem it solves:** Implementers stuck writing dummy/throwing methods; clients recompiled or broken by changes to methods they never call.
- **Avoid over-applying when:** Every method ends up in its own interface. Group methods that are always used together.

## Key Takeaways
- Design interfaces from the **client's** point of view (role interfaces), not the implementer's.
- Fat interfaces often cause [LSP](Liskov-Substitution-Principle.md) violations (`UnsupportedOperationException`).
- A class can implement **multiple** small interfaces, so nothing is lost by splitting.
- ISP is [SRP](Single-Responsibility-Principle.md) applied to interfaces.
- JDK example: `Runnable`, `Comparable`, `AutoCloseable` are tiny, focused interfaces.

## Violation
```java
interface Machine {
    void print(String doc);
    void scan(String doc);
    void fax(String doc);
}

class BasicPrinter implements Machine {
    public void print(String doc) { System.out.println("Print " + doc); }
    public void scan(String doc) { throw new UnsupportedOperationException(); } // forced
    public void fax(String doc)  { throw new UnsupportedOperationException(); } // forced
}
```

## Corrected Design
```java
interface Printer { void print(String doc); }
interface Scanner { void scan(String doc); }
interface Fax     { void fax(String doc); }

class BasicPrinter implements Printer {                 // implements only what it supports
    public void print(String doc) { System.out.println("Print " + doc); }
}

class OfficeMachine implements Printer, Scanner, Fax {  // combines roles when it really has them
    public void print(String doc) { System.out.println("Print " + doc); }
    public void scan(String doc)  { System.out.println("Scan " + doc); }
    public void fax(String doc)   { System.out.println("Fax " + doc); }
}

class Main {
    static void printReport(Printer p) { p.print("report.pdf"); } // depends only on what it needs
    public static void main(String[] args) {
        printReport(new BasicPrinter());
        printReport(new OfficeMachine());
    }
}
```

**Key benefit:** Implementers aren't forced into fake methods, and clients are insulated from unrelated changes.

## Interview Questions
**Q1. What is a "fat interface" and why is it bad?** *(most common)*
An interface with methods serving different clients. Implementers must stub unused methods, and changes ripple to clients that don't care.

**Q2. How is ISP different from SRP?**
SRP is about a class's reasons to change; ISP is about what a client is forced to depend on. ISP is effectively SRP for interfaces.

**Q3. How does ISP relate to LSP?**
Fat interfaces push implementers into throwing for unsupported methods, which breaks substitutability. Splitting fixes both.

**Q4. Can ISP be overdone?**
Yes. One-method interfaces for everything fragment the design. Group by cohesive client usage.

**Q5. Give a JDK example of good segregation.**
`Iterable` vs `Collection` vs `List`. Code needing only iteration accepts `Iterable`.

## Common Pitfalls
- Splitting interfaces by implementer convenience instead of client needs.
- Thinking default methods in Java 8+ solve fat interfaces. They hide the stubs; they don't remove the coupling.
- Creating dozens of single-method interfaces with no clear role names.

## Related Topics
- [Liskov Substitution Principle](Liskov-Substitution-Principle.md)
- [Single Responsibility Principle](Single-Responsibility-Principle.md)
- [Class vs Interface vs Abstract Class](../Core-Concepts/Class-vs-Interface-vs-Abstract-Class.md)

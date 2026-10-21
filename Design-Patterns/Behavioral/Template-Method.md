# Template Method

> **Category:** Behavioral
> **Intent:** Define the skeleton of an algorithm in a base class and let subclasses override specific steps without changing the structure.

## Quick Summary
- **Definition:** A `final` method in an abstract class calls a fixed sequence of steps; some steps are abstract (must override) or hooks (may override).
- **Problem it solves:** Duplicated algorithm structure across classes that differ only in a few steps.
- **Use when:** Several classes share the same workflow (parse → validate → save) but differ in specific steps.
- **Avoid when:** Steps vary independently or need runtime swapping. Prefer [Strategy](Strategy.md) (composition).

## Key Takeaways
- Make the template method **`final`** so subclasses can't change the algorithm's order.
- **Abstract steps** = mandatory customization; **hooks** = optional, with default behavior.
- **Hollywood Principle:** "Don't call us, we'll call you." The base class calls subclass methods.
- Inheritance-based, so it carries inheritance's downsides (tight coupling, fragile base class). See [Composition vs Inheritance](../../Core-Concepts/Composition-vs-Inheritance.md).
- JDK/framework examples: `AbstractList` (implement `get()`/`size()`), `HttpServlet.service()` → `doGet()`/`doPost()`, Spring's `JdbcTemplate` (callback flavor).

## Pattern Profile
- **Participants:**
  - `AbstractClass` (`DataExporter`) — defines the `final` template method and step methods.
  - `ConcreteClass` (`CsvExporter`, `JsonExporter`) — implements the variable steps.
- **Real-world use:** Report/export pipelines, test frameworks (setUp → test → tearDown), game turn loops, servlet request handling.
- **Before:** `CsvExporter` and `JsonExporter` each duplicate fetch → format → write, and drift apart over time.
- **After:** The shared flow lives once in the base class; subclasses only implement `format()`.
- **Trade-off:** Tightly couples subclasses to the base class; changing the skeleton affects all subclasses; limited to one axis of variation.
- **vs. [Strategy](Strategy.md):** Template Method varies **parts** of an algorithm via **inheritance** (compile time). Strategy swaps the **whole** algorithm via **composition** (runtime).

## Java Example
```java
import java.util.List;

abstract class DataExporter {                              // AbstractClass
    public final void export() {                           // template method: fixed order
        List<String> rows = fetchData();
        String output = format(rows);                      // varies per subclass
        if (shouldCompress()) output = "[gz]" + output;    // hook
        System.out.println(output);
    }

    private List<String> fetchData() { return List.of("asha,30", "ravi,25"); } // shared step
    protected abstract String format(List<String> rows);                        // required step
    protected boolean shouldCompress() { return false; }                        // hook: optional
}

class CsvExporter extends DataExporter {                   // ConcreteClass
    protected String format(List<String> rows) { return String.join("\n", rows); }
}

class JsonExporter extends DataExporter {                  // ConcreteClass
    protected String format(List<String> rows) { return "[\"" + String.join("\",\"", rows) + "\"]"; }
    @Override protected boolean shouldCompress() { return true; }  // override hook
}

class Main {
    public static void main(String[] args) {
        new CsvExporter().export();
        new JsonExporter().export();
    }
}
```

## Interview Questions
**Q1. Template Method vs Strategy?** *(most common)*
Template Method uses inheritance to vary steps of a fixed algorithm. Strategy uses composition to swap entire algorithms at runtime. Prefer Strategy when variations are independent or runtime-selected.

**Q2. Why make the template method `final`?**
To stop subclasses from changing the algorithm's structure; they may customize only the designated steps.

**Q3. What is a hook method?**
A step with a default (often empty) implementation that subclasses may optionally override to extend behavior at a defined point.

**Q4. What is the Hollywood Principle?**
"Don't call us, we'll call you." High-level base classes call low-level subclass methods, not vice versa.

**Q5. Examples in Java?**
`HttpServlet.service()` dispatching to `doGet()`/`doPost()`; `AbstractList`; JUnit's lifecycle.

**Q6. What are its drawbacks?**
Inheritance coupling, the fragile base class problem, and a combinatorial explosion if multiple steps vary independently.

## Common Pitfalls
- Not marking the template method `final`.
- Too many abstract steps, forcing subclasses to implement things they don't care about.
- Using Template Method where step variations are independent (use Strategy).

## Related Topics
- [Strategy](Strategy.md)
- [Factory Method](../Creational/Factory-Method.md) — often a step inside a template method
- [Composition vs Inheritance](../../Core-Concepts/Composition-vs-Inheritance.md)

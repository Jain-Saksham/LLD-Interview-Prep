# Builder

> **Category:** Creational
> **Intent:** Separate the construction of a complex object from its representation so it can be built step by step.

## Quick Summary
- **Definition:** A helper object collects parameters through readable, chained calls, then produces the final (often immutable) object with `build()`.
- **Problem it solves:** **Telescoping constructors** (`new Pizza(size, true, false, true, null)`) and mutable half-initialized objects from setters.
- **Use when:** Many parameters, especially optional ones; you want immutability; validation should happen before the object exists.
- **Avoid when:** The object has 2–3 required fields. A constructor is simpler.

## Key Takeaways
- Fluent API: each setter returns `this` for chaining.
- `build()` is the single place to **validate** invariants before creating the object.
- Enables **immutable** objects (all `final` fields, no setters).
- Required params → builder constructor; optional params → chained methods.
- The GoF version adds a `Director` that runs a fixed build sequence. In modern Java, the fluent "Effective Java" builder is what's usually asked.
- JDK examples: `StringBuilder`, `HttpRequest.newBuilder()`, `Stream.builder()`. Lombok `@Builder` generates it.

## Pattern Profile
- **Participants:**
  - `Product` (`Pizza`) — the complex, immutable object.
  - `Builder` (`Pizza.Builder`) — accumulates configuration and builds the product.
  - `Director` *(optional)* — encapsulates common build sequences (e.g., `margherita()`).
- **Real-world use:** HTTP requests, SQL query builders, test data fixtures, config objects.
- **Before:** Constructor with 6 params where callers swap two booleans without noticing; or setters leaving objects in invalid states.
- **After:** Self-documenting construction, validated once, resulting in an immutable object.
- **Trade-off:** Extra class and duplicated field declarations; more code for simple objects.
- **vs. [Abstract Factory](Abstract-Factory.md):** Builder constructs **one complex object step by step**. Abstract Factory returns **families of objects immediately** in one call.

## Java Example
```java
class Pizza {                                      // Product: immutable
    private final String size;                     // required
    private final boolean cheese;                  // optional
    private final boolean mushrooms;               // optional

    private Pizza(Builder b) {                     // only the Builder can construct
        this.size = b.size;
        this.cheese = b.cheese;
        this.mushrooms = b.mushrooms;
    }

    @Override public String toString() {
        return size + " pizza [cheese=" + cheese + ", mushrooms=" + mushrooms + "]";
    }

    static class Builder {                         // Builder
        private final String size;                 // required → constructor param
        private boolean cheese;                    // optional → defaults to false
        private boolean mushrooms;

        Builder(String size) { this.size = size; }

        Builder cheese()    { this.cheese = true; return this; }    // return this → chaining
        Builder mushrooms() { this.mushrooms = true; return this; }

        Pizza build() {
            if (size == null || size.isBlank())     // validate before the object exists
                throw new IllegalStateException("size is required");
            return new Pizza(this);
        }
    }
}

class Main {
    public static void main(String[] args) {
        Pizza pizza = new Pizza.Builder("Large")
                .cheese()
                .mushrooms()
                .build();
        System.out.println(pizza);   // Large pizza [cheese=true, mushrooms=true]
    }
}
```

## Interview Questions
**Q1. What problem does Builder solve?** *(most common)*
Telescoping constructors and unreadable calls with many (especially optional or same-typed) parameters. It also enables immutability and centralized validation.

**Q2. Builder vs setters (JavaBeans)?**
Setters allow a half-built, mutable object to escape and can't enforce invariants across fields. Builder validates in `build()` and produces an immutable object.

**Q3. Why is the product's constructor private?**
To force construction through the builder so validation can't be bypassed.

**Q4. What's the role of the Director?**
It encapsulates reusable construction sequences (e.g., "build a Margherita"). It's optional and often omitted in fluent builders.

**Q5. Is Builder thread-safe?**
The builder itself usually isn't (it's mutable and meant for one thread). The built immutable product is thread-safe.

**Q6. Builder vs Factory?**
A factory decides *which* object to create in one call. A builder controls *how* a single complex object is assembled over many calls.

## Common Pitfalls
- Using a builder for a 2-field class.
- Skipping validation in `build()`, which loses a main benefit.
- Leaving the product mutable (public setters), which defeats immutability.

## Related Topics
- [Abstract Factory](Abstract-Factory.md)
- [Composite](../Structural/Composite.md) — builders are handy for assembling composite trees
- [Prototype](Prototype.md)

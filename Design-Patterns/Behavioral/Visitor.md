# Visitor

> **Category:** Behavioral
> **Intent:** Represent an operation to be performed on elements of an object structure, letting you add new operations without changing the element classes.

## Quick Summary
- **Definition:** Elements expose `accept(visitor)`, which calls back `visitor.visit(this)`. Each operation is a visitor class with one `visit` method per element type.
- **Problem it solves:** Adding operations (export, tax calculation, rendering) to a stable class hierarchy without polluting every class or using `instanceof` chains.
- **Use when:** The element hierarchy is **stable** but **operations change often** (compilers/ASTs, document exporters, shopping-cart pricing).
- **Avoid when:** New element types are added frequently. Every visitor must then change.

## Key Takeaways
- Uses **double dispatch**: the method run depends on both the visitor type and the element type. Java only has single dispatch natively.
- **Easy to add operations, hard to add element types.** The mirror image of normal OOP.
- Keeps unrelated operations out of element classes ([SRP](../../SOLID/Single-Responsibility-Principle.md)).
- Visitors may need access to element internals, which can weaken encapsulation.
- Modern Java alternative: **sealed interfaces + pattern-matching `switch`** (Java 21) gives exhaustive type switches without the boilerplate.
- JDK: `java.nio.file.FileVisitor` (`Files.walkFileTree`), annotation processing `ElementVisitor`.

## Pattern Profile
- **Participants:**
  - `Visitor` (`CartVisitor`) — declares `visit(X)` for each element type.
  - `ConcreteVisitor` (`PriceCalculator`) — implements one operation across all types.
  - `Element` (`CartItem`) — declares `accept(Visitor)`.
  - `ConcreteElement` (`Book`, `Electronics`) — implements `accept` by calling `visitor.visit(this)`.
- **Real-world use:** Compiler AST passes (type check, optimize, codegen), exporting a document to PDF/HTML, tax/shipping calculations over cart items, file tree walking.
- **Before:** Adding "calculate tax" means adding a method to every item class, or writing `if (item instanceof Book) ...`.
- **After:** One new `TaxVisitor` class; item classes untouched.
- **Trade-off:** Adding a new element type forces changes in every visitor; more indirection.
- **vs. [Iterator](Iterator.md):** Iterator defines **how to traverse**. Visitor defines **what to do** with each (heterogeneous) element. They often work together. Also vs [Strategy](Strategy.md): Strategy varies one algorithm; Visitor varies operations across many types.

## Java Example
```java
import java.util.List;

interface CartVisitor {                                   // Visitor: one visit per element type
    double visit(Book book);
    double visit(Electronics item);
}

interface CartItem {                                      // Element
    double accept(CartVisitor visitor);
}

class Book implements CartItem {                          // ConcreteElement
    final double price;
    Book(double price) { this.price = price; }
    public double accept(CartVisitor v) { return v.visit(this); }   // double dispatch
}

class Electronics implements CartItem {                   // ConcreteElement
    final double price;
    Electronics(double price) { this.price = price; }
    public double accept(CartVisitor v) { return v.visit(this); }
}

class TaxCalculator implements CartVisitor {              // ConcreteVisitor: one operation
    public double visit(Book b)        { return b.price * 0.05; }  // books: 5%
    public double visit(Electronics e) { return e.price * 0.18; }  // electronics: 18%
}

class Main {
    public static void main(String[] args) {
        List<CartItem> cart = List.of(new Book(500), new Electronics(10000));
        CartVisitor tax = new TaxCalculator();

        double totalTax = 0;
        for (CartItem item : cart) totalTax += item.accept(tax);  // no instanceof
        System.out.println("Total tax: " + totalTax);               // 25.0 + 1800.0 = 1825.0
    }
}
```

## Interview Questions
**Q1. What is double dispatch and why does Visitor need it?** *(most common)*
Choosing the method by the runtime types of two objects. Java picks overloads by static type, so `element.accept(visitor)` dispatches on the element, then `visitor.visit(this)` dispatches on the visitor with the correct static type.

**Q2. When is Visitor a bad choice?**
When the element hierarchy changes often. Each new element type requires a new method in every visitor.

**Q3. Visitor vs adding a method to each class?**
Adding methods is natural when operations are few and stable. Visitor wins when operations change frequently and you don't want unrelated logic (export, tax, render) inside domain classes.

**Q4. How do modern Java features affect Visitor?**
Sealed interfaces + pattern-matching `switch` (Java 21) give exhaustive, compiler-checked type switches, often replacing Visitor with less boilerplate.

**Q5. Real-world examples?**
Compilers/ASTs (javac, ANTLR), `Files.walkFileTree(FileVisitor)`, document exporters.

**Q6. Does Visitor break encapsulation?**
Potentially. Visitors often need element data, so elements may expose more than they otherwise would.

## Common Pitfalls
- Not being able to explain double dispatch clearly.
- Using Visitor on a hierarchy that frequently gains new types.
- Overloading `visit` but calling it with a base-type reference, which resolves the wrong overload at compile time.

## Related Topics
- [Composite](../Structural/Composite.md) — visitors often walk composite trees
- [Iterator](Iterator.md)
- [Interpreter](Interpreter.md) — interpret operations can be visitors
- [Open/Closed Principle](../../SOLID/Open-Closed-Principle.md) — open for new operations

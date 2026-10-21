# Abstract Factory

> **Category:** Creational
> **Intent:** Provide an interface for creating families of related objects without specifying their concrete classes.

## Quick Summary
- **Definition:** A factory object with multiple creation methods, one per product type. Each concrete factory produces a consistent family.
- **Problem it solves:** Ensuring products that must be used together (e.g., Windows button + Windows checkbox) are never mixed with another family.
- **Use when:** The system must work with one of several product families, chosen at runtime or config time.
- **Avoid when:** There's just one product type (use [Factory Method](Factory-Method.md)) or new product *types* are added frequently.

## Key Takeaways
- Guarantees **family consistency**: you can't accidentally get a Mac button with a Windows checkbox.
- Swapping the entire family = swapping one factory object.
- **Easy to add a new family**; **hard to add a new product type** (every factory must change). This is the key trade-off.
- Concrete factories are often [Singletons](Singleton.md) or injected via [DI](../../Core-Concepts/Dependency-Injection.md).
- Each creation method is typically a factory method.

## Pattern Profile
- **Participants:**
  - `AbstractFactory` (`UIFactory`) — declares create methods for each product.
  - `ConcreteFactory` (`WindowsFactory`, `MacFactory`) — creates one family.
  - `AbstractProduct` (`Button`, `Checkbox`) — product interfaces.
  - `ConcreteProduct` (`WindowsButton`, `MacCheckbox`...) — family-specific implementations.
  - `Client` (`Dialog`) — uses only abstract interfaces.
- **Real-world use:** Cross-platform UI toolkits, DB-vendor-specific DAOs, themed components, cloud-provider SDK abstractions (AWS vs GCP storage + queue).
- **Before:** `if (os == WINDOWS)` checks repeated at every widget creation; families can mix.
- **After:** Choose the factory once; all products come out consistent.
- **Trade-off:** Adding a new product type (e.g., `Slider`) requires changing the interface and **every** concrete factory.
- **vs. [Factory Method](Factory-Method.md):** Abstract Factory = an **object** creating **many related** products (composition). Factory Method = a **method** overridden by subclasses to create **one** product (inheritance).

## Java Example
```java
interface Button   { void render(); }                     // AbstractProduct
interface Checkbox { void render(); }                     // AbstractProduct

class WindowsButton   implements Button   { public void render() { System.out.println("[Win Button]"); } }
class WindowsCheckbox implements Checkbox { public void render() { System.out.println("[Win Checkbox]"); } }
class MacButton       implements Button   { public void render() { System.out.println("(Mac Button)"); } }
class MacCheckbox     implements Checkbox { public void render() { System.out.println("(Mac Checkbox)"); } }

interface UIFactory {                                     // AbstractFactory
    Button createButton();
    Checkbox createCheckbox();
}

class WindowsFactory implements UIFactory {               // ConcreteFactory: Windows family
    public Button createButton()     { return new WindowsButton(); }
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}

class MacFactory implements UIFactory {                   // ConcreteFactory: Mac family
    public Button createButton()     { return new MacButton(); }
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}

class Dialog {                                            // Client: knows only abstractions
    private final Button button;
    private final Checkbox checkbox;

    Dialog(UIFactory factory) {                           // the whole family comes from one factory
        this.button = factory.createButton();
        this.checkbox = factory.createCheckbox();
    }

    void render() { button.render(); checkbox.render(); }
}

class Main {
    public static void main(String[] args) {
        String os = "mac";                                // e.g., System.getProperty("os.name")
        UIFactory factory = os.equals("mac") ? new MacFactory() : new WindowsFactory();
        new Dialog(factory).render();
    }
}
```

## Interview Questions
**Q1. Abstract Factory vs Factory Method?** *(most common)*
Abstract Factory creates families of related objects through a factory object (composition). Factory Method creates one product through subclass overriding (inheritance).

**Q2. What's the main weakness of Abstract Factory?**
Adding a new product type forces changes to the abstract factory and all concrete factories. It favors adding families over adding products.

**Q3. How does it enforce consistency?**
The client gets every product from the same factory instance, so it can't mix families.

**Q4. Where is the concrete factory chosen?**
Once, at the composition root: config, environment, or a DI container. Business code never decides.

**Q5. Real-world example?**
`javax.xml.parsers.DocumentBuilderFactory`, cross-platform UI kits, or a `CloudFactory` producing matching `Storage` + `Queue` for AWS vs GCP.

**Q6. Can Abstract Factory be combined with other patterns?**
Yes. Concrete factories are often Singletons; creation methods may use Factory Method or [Prototype](Prototype.md).

## Common Pitfalls
- Using Abstract Factory for a single product. That's over-engineering.
- Not mentioning the "hard to add new product types" trade-off.
- Letting client code still check the platform, which defeats the purpose.

## Related Topics
- [Factory Method](Factory-Method.md)
- [Builder](Builder.md) — focuses on step-by-step construction of one complex object
- [Bridge](../Structural/Bridge.md) — Abstract Factory can create and configure a Bridge

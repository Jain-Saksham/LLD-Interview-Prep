# Composite

> **Category:** Structural
> **Intent:** Compose objects into tree structures and let clients treat individual objects and compositions uniformly.

## Quick Summary
- **Definition:** Leaves and containers share one interface. Containers hold children (leaves or other containers) and delegate operations recursively.
- **Problem it solves:** Client code full of `if (node instanceof Folder) ... else ...` when working with part-whole hierarchies.
- **Use when:** The data is naturally a tree (file systems, org charts, UI widgets, menus) and operations apply to both parts and wholes.
- **Avoid when:** The structure isn't hierarchical, or leaves and containers have very different behavior.

## Key Takeaways
- Key idea: **recursion through a common interface**. `size()` on a folder = sum of `size()` of its children.
- Design choice: put `add()/remove()` in the common interface (**transparency**, but leaves must reject them) or only in the composite (**safety**, but clients need type knowledge). Prefer safety in Java.
- Makes adding new node types easy ([OCP](../../SOLID/Open-Closed-Principle.md)).
- Often paired with [Iterator](../Behavioral/Iterator.md) (traversal) and [Visitor](../Behavioral/Visitor.md) (operations over the tree).
- Real-world: `java.awt.Container` holds `Component`s; HTML DOM; menu with submenus.

## Pattern Profile
- **Participants:**
  - `Component` (`FileSystemItem`) — common interface.
  - `Leaf` (`File`) — no children; does the real work.
  - `Composite` (`Folder`) — holds children; delegates and aggregates.
- **Real-world use:** File explorers, org hierarchies (total salary of a department), nested UI layouts, product bundles.
- **Before:** Client must distinguish files from folders and recurse manually.
- **After:** Client calls `item.size()` on anything.
- **Trade-off:** Hard to restrict which components can be children (e.g., "only Files in this folder") at compile time; a uniform interface can be overly general.
- **vs. [Decorator](Decorator.md):** Composite holds **many** children to **aggregate** results. Decorator wraps **one** object to **add** behavior.

## Java Example
```java
import java.util.ArrayList;
import java.util.List;

interface FileSystemItem {                         // Component
    long size();
    void print(String indent);
}

class File implements FileSystemItem {             // Leaf
    private final String name;
    private final long bytes;
    File(String name, long bytes) { this.name = name; this.bytes = bytes; }

    public long size() { return bytes; }
    public void print(String indent) { System.out.println(indent + name + " (" + bytes + ")"); }
}

class Folder implements FileSystemItem {           // Composite
    private final String name;
    private final List<FileSystemItem> children = new ArrayList<>();
    Folder(String name) { this.name = name; }

    Folder add(FileSystemItem item) { children.add(item); return this; } // only on Composite (safe design)

    public long size() {                           // recursion: sum of children
        long total = 0;
        for (FileSystemItem c : children) total += c.size();
        return total;
    }

    public void print(String indent) {
        System.out.println(indent + name + "/");
        for (FileSystemItem c : children) c.print(indent + "  ");
    }
}

class Main {
    public static void main(String[] args) {
        Folder root = new Folder("root")
                .add(new File("a.txt", 100))
                .add(new Folder("docs").add(new File("cv.pdf", 300)));
        root.print("");
        System.out.println("Total: " + root.size());   // 400, same call for file or folder
    }
}
```

## Interview Questions
**Q1. What problem does Composite solve?** *(most common)*
It lets clients treat single objects and groups uniformly in a tree, removing type checks and manual recursion from client code.

**Q2. Transparency vs safety: where do `add()/remove()` go?**
In the Component interface: uniform, but leaves must throw (an [LSP](../../SOLID/Liskov-Substitution-Principle.md) smell). In Composite only: type-safe, but clients need to know the type to add children. Most Java designs choose safety.

**Q3. Composite vs Decorator?**
Both use recursive composition. Composite aggregates many children; Decorator wraps exactly one to add behavior.

**Q4. How would you compute the total salary of a department hierarchy?**
`Employee` (leaf) returns its salary. `Department` (composite) returns the sum of `getSalary()` over its members.

**Q5. How do you prevent cycles (a folder containing itself)?**
Validate in `add()` by checking that the new child isn't an ancestor, or maintain parent pointers.

## Common Pitfalls
- Leaves implementing `add()` by silently doing nothing (hides bugs).
- Client code still using `instanceof Folder`, which defeats the pattern.
- Ignoring cycles or very deep recursion.

## Related Topics
- [Decorator](Decorator.md)
- [Iterator](../Behavioral/Iterator.md)
- [Visitor](../Behavioral/Visitor.md)
- [Interpreter](../Behavioral/Interpreter.md) — the expression tree is a Composite
- [Flyweight](Flyweight.md) — leaves are often shared flyweights

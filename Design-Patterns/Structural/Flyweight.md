# Flyweight

> **Category:** Structural
> **Intent:** Use sharing to support large numbers of fine-grained objects efficiently.

## Quick Summary
- **Definition:** Split object state into **intrinsic** (shared, immutable) and **extrinsic** (context-specific, passed in). Share one object per unique intrinsic state via a factory/cache.
- **Problem it solves:** Memory blow-up from millions of near-identical objects (characters in an editor, trees in a game, map markers).
- **Use when:** Huge numbers of objects, most state can be shared, and memory is a measured problem.
- **Avoid when:** Object counts are small or memory isn't a bottleneck. It complicates code for no gain.

## Key Takeaways
- **Intrinsic** state: stored inside the flyweight, shared, **must be immutable**.
- **Extrinsic** state: supplied by the client at call time (position, size, context).
- A **factory with a cache** (`Map`) guarantees one instance per intrinsic key.
- Trades **memory for CPU/complexity** (lookups, passing extrinsic state around).
- JDK examples: `Integer.valueOf()` caches -128..127, the `String` pool, `Boolean.valueOf()`.

## Pattern Profile
- **Participants:**
  - `Flyweight` (`TreeType`) — holds intrinsic state; operations take extrinsic state as params.
  - `FlyweightFactory` (`TreeFactory`) — creates/caches shared flyweights.
  - `Client/Context` (`Tree`) — stores extrinsic state and a reference to a flyweight.
- **Real-world use:** Text editor glyphs, game particles/trees, map icons, connection to shared style objects in rendering.
- **Before:** 1,000,000 trees each store name, color, and texture: massive duplication.
- **After:** A few `TreeType` objects shared; each tree stores only x, y + a reference.
- **Trade-off:** Code becomes harder to read; extrinsic state must be managed externally; shared objects must be thread-safe (immutable).
- **vs. [Singleton](../Creational/Singleton.md):** Singleton = exactly **one** instance of a class. Flyweight = **one instance per unique intrinsic state**, possibly many.

## Java Example
```java
import java.util.HashMap;
import java.util.Map;

final class TreeType {                                  // Flyweight: intrinsic, immutable
    private final String name;
    private final String color;
    TreeType(String name, String color) { this.name = name; this.color = color; }

    void draw(int x, int y) {                           // extrinsic state passed in
        System.out.println(name + "(" + color + ") at " + x + "," + y);
    }
}

class TreeFactory {                                     // FlyweightFactory
    private static final Map<String, TreeType> cache = new HashMap<>();

    static TreeType get(String name, String color) {
        return cache.computeIfAbsent(name + ":" + color, k -> new TreeType(name, color)); // share
    }
    static int typesCreated() { return cache.size(); }
}

class Tree {                                            // Context: extrinsic state + flyweight ref
    private final int x, y;
    private final TreeType type;
    Tree(int x, int y, TreeType type) { this.x = x; this.y = y; this.type = type; }
    void draw() { type.draw(x, y); }
}

class Main {
    public static void main(String[] args) {
        Tree[] forest = {
            new Tree(1, 2, TreeFactory.get("Oak", "green")),
            new Tree(5, 7, TreeFactory.get("Oak", "green")),    // reuses the same TreeType
            new Tree(3, 9, TreeFactory.get("Pine", "dark")),
        };
        for (Tree t : forest) t.draw();
        System.out.println("TreeTypes created: " + TreeFactory.typesCreated()); // 2, not 3
    }
}
```

## Interview Questions
**Q1. What's intrinsic vs extrinsic state?** *(most common)*
Intrinsic is shared and context-independent, stored in the flyweight and immutable. Extrinsic varies per use and is passed in by the client.

**Q2. Why must flyweights be immutable?**
They're shared. Mutating one changes every object using it, and it's unsafe across threads.

**Q3. Give JDK examples.**
`Integer.valueOf()` caching -128..127 (which is why `==` on small `Integer`s "works" but fails for larger values), and the `String` pool.

**Q4. Flyweight vs Singleton?**
Singleton enforces one instance total. Flyweight shares one instance per distinct intrinsic state and is about memory, not uniqueness.

**Q5. When is Flyweight not worth it?**
When object counts are modest. The complexity and lookup cost outweigh the memory saved. Measure first.

**Q6. Flyweight vs caching/object pool?**
A cache/pool reuses objects that are checked out exclusively and may be mutable (connection pool). Flyweights are shared simultaneously and must be immutable.

## Common Pitfalls
- Making flyweights mutable.
- Applying it without evidence of memory pressure.
- Using a non-thread-safe cache (`HashMap`) when factories are accessed concurrently. Use `ConcurrentHashMap` there.

## Related Topics
- [Singleton](../Creational/Singleton.md)
- [Composite](Composite.md) — leaf nodes are often flyweights
- [Prototype](../Creational/Prototype.md) — copies instead of sharing

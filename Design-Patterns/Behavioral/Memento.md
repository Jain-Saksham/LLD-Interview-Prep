# Memento

> **Category:** Behavioral
> **Intent:** Capture and externalize an object's internal state so it can be restored later, without violating encapsulation.

## Quick Summary
- **Definition:** The originator creates an opaque snapshot (memento) of its state; a caretaker stores mementos and hands them back for restoration.
- **Problem it solves:** Implementing undo/checkpoints without exposing the object's private fields to the outside world.
- **Use when:** Undo/redo, checkpoints, rollback of failed operations, game save points.
- **Avoid when:** State is large and snapshots are frequent (memory cost), or the inverse operation is easy (use [Command](Command.md) undo instead).

## Key Takeaways
- Only the **originator** can read a memento's contents. The caretaker treats it as opaque, which preserves encapsulation.
- Mementos must be **immutable** snapshots.
- Memory is the main cost. Mitigate with history limits, incremental/diff snapshots, or compression.
- Deep vs shallow copy matters for mutable fields (see [Prototype](../Creational/Prototype.md)).
- In Java, a `private static` nested class or a `record` with package-private access works well as a memento.

## Pattern Profile
- **Participants:**
  - `Originator` (`Editor`) — creates and restores from mementos.
  - `Memento` (`Editor.Snapshot`) — immutable, opaque state snapshot.
  - `Caretaker` (`History`) — stores mementos; never inspects them.
- **Real-world use:** Editor undo, game save/load, form drafts, transaction rollback, VM snapshots.
- **Before:** An undo manager reads and writes the editor's private fields via getters/setters, which breaks encapsulation.
- **After:** The editor produces and consumes its own snapshots; history just stores them.
- **Trade-off:** High memory usage for large or frequent snapshots.
- **vs. [Command](Command.md):** Command undoes by running the **inverse operation**. Memento undoes by **restoring a saved state**. Command is cheaper in memory; Memento is simpler when inverse logic is hard.

## Java Example
```java
import java.util.ArrayDeque;
import java.util.Deque;

class Editor {                                            // Originator
    private String content = "";
    private int cursor = 0;

    void type(String text) { content += text; cursor = content.length(); }

    Snapshot save() { return new Snapshot(content, cursor); }       // create memento
    void restore(Snapshot s) { content = s.content; cursor = s.cursor; } // only Editor reads it

    @Override public String toString() { return "'" + content + "' cursor=" + cursor; }

    static final class Snapshot {                         // Memento: immutable, opaque outside Editor
        private final String content;
        private final int cursor;
        private Snapshot(String content, int cursor) { this.content = content; this.cursor = cursor; }
    }
}

class History {                                           // Caretaker: stores, never inspects
    private final Deque<Editor.Snapshot> stack = new ArrayDeque<>();
    void push(Editor.Snapshot s) { stack.push(s); }
    Editor.Snapshot pop() { return stack.pop(); }
}

class Main {
    public static void main(String[] args) {
        Editor editor = new Editor();
        History history = new History();

        editor.type("Hello");
        history.push(editor.save());                      // checkpoint
        editor.type(" World");
        System.out.println(editor);                       // 'Hello World' cursor=11

        editor.restore(history.pop());                    // undo
        System.out.println(editor);                       // 'Hello' cursor=5
    }
}
```

## Interview Questions
**Q1. How does Memento preserve encapsulation?** *(most common)*
The memento's fields are private and readable only by the originator (e.g., a nested class). The caretaker holds it as an opaque token.

**Q2. Memento vs Command for undo?**
Command reverses via inverse operations (memory-light, but needs reversible logic). Memento restores full snapshots (simple, but memory-heavy). Many editors combine them: commands that store mementos.

**Q3. How do you limit memory usage?**
Cap history size, store diffs instead of full state, compress, or snapshot only at intervals.

**Q4. Why should mementos be immutable?**
So a stored snapshot can't be altered after the fact, which would corrupt undo history.

**Q5. Memento vs Prototype?**
Both copy state. Prototype creates a new working object. Memento stores a snapshot purely for later restoration of the same object.

## Common Pitfalls
- Exposing memento internals via public getters, which defeats encapsulation.
- Shallow-copying mutable fields into the memento.
- Unbounded history stacks causing memory issues.

## Related Topics
- [Command](Command.md)
- [Prototype](../Creational/Prototype.md)
- [Iterator](Iterator.md) — can use a memento to save iteration state

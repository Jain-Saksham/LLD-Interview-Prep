# Iterator

> **Category:** Behavioral
> **Intent:** Provide a way to access elements of a collection sequentially without exposing its underlying representation.

## Quick Summary
- **Definition:** A separate object tracks traversal position and exposes `hasNext()` / `next()`.
- **Problem it solves:** Clients depending on a collection's internals (array index, linked nodes, tree structure) to traverse it.
- **Use when:** Custom collections, multiple traversal orders, or lazily generated sequences.
- **Avoid when:** You're using standard Java collections. They already provide iterators.

## Key Takeaways
- Built into Java: implement `Iterable<T>` → works with the **for-each** loop.
- Separates **traversal** from the **collection** ([SRP](../../SOLID/Single-Responsibility-Principle.md)); multiple independent iterators can run at once.
- **Fail-fast** iterators (`ArrayList`, `HashMap`) throw `ConcurrentModificationException` if the collection is structurally modified during iteration (except via `iterator.remove()`).
- **Fail-safe / weakly consistent** iterators (`CopyOnWriteArrayList`, `ConcurrentHashMap`) don't throw, but may not reflect concurrent changes.
- Internal iteration (`forEach(lambda)`, streams) vs external iteration (`hasNext/next`).

## Pattern Profile
- **Participants:**
  - `Iterator` (`java.util.Iterator`) — traversal interface.
  - `ConcreteIterator` (`PlaylistIterator`) — tracks position.
  - `Aggregate` (`Iterable`) — declares `iterator()`.
  - `ConcreteAggregate` (`Playlist`) — returns its iterator.
- **Real-world use:** Paginated API results, music playlists, tree traversals (in-order/BFS), database cursors.
- **Before:** Clients write `for (int i = 0; i < playlist.songs.length; i++)`, coupled to the array.
- **After:** `for (String song : playlist)`. Internal storage can change freely.
- **Trade-off:** Extra class for simple collections; external iterators are stateful and not thread-safe.
- **vs. [Visitor](Visitor.md):** Iterator answers **how to traverse**. Visitor answers **what operation to perform** on each element (often of different types).

## Java Example
```java
import java.util.Iterator;
import java.util.NoSuchElementException;

class Playlist implements Iterable<String> {             // ConcreteAggregate
    private final String[] songs;                        // internal representation is hidden
    Playlist(String... songs) { this.songs = songs; }

    @Override
    public Iterator<String> iterator() {                 // factory method for iterators
        return new Iterator<>() {                        // ConcreteIterator
            private int index = 0;                       // traversal state lives here

            public boolean hasNext() { return index < songs.length; }

            public String next() {
                if (!hasNext()) throw new NoSuchElementException();
                return songs[index++];
            }
        };
    }
}

class Main {
    public static void main(String[] args) {
        Playlist playlist = new Playlist("Song A", "Song B", "Song C");
        for (String song : playlist) {                   // for-each works via Iterable
            System.out.println(song);
        }
    }
}
```

## Interview Questions
**Q1. Fail-fast vs fail-safe iterators?** *(most common)*
Fail-fast (`ArrayList`, `HashMap`) detect structural modification via `modCount` and throw `ConcurrentModificationException`. Fail-safe/weakly consistent (`CopyOnWriteArrayList`, `ConcurrentHashMap`) iterate over a snapshot or tolerate changes without throwing.

**Q2. How do you safely remove elements while iterating?**
Use `iterator.remove()`, or `collection.removeIf(predicate)`. Calling `list.remove()` inside a for-each throws CME.

**Q3. `Iterable` vs `Iterator`?**
`Iterable` is the collection that can produce iterators (`iterator()`). `Iterator` is the cursor doing the traversal (`hasNext()`, `next()`).

**Q4. Internal vs external iteration?**
External: the client controls the loop (`hasNext/next`). Internal: the collection controls it and you pass behavior (`forEach`, streams), which enables parallelism and laziness.

**Q5. Why use an iterator instead of exposing the list?**
It preserves encapsulation. Callers can't modify internals, and you can change the data structure without breaking them.

## Common Pitfalls
- Not throwing `NoSuchElementException` from `next()` when exhausted.
- Modifying the collection during for-each.
- Building custom iterators for standard collections that already have them.

## Related Topics
- [Composite](../Structural/Composite.md) — iterating over trees
- [Visitor](Visitor.md)
- [Factory Method](../Creational/Factory-Method.md) — `iterator()` is a factory method

# Command

> **Category:** Behavioral
> **Intent:** Encapsulate a request as an object, allowing parameterization, queuing, logging, and undo of operations.

## Quick Summary
- **Definition:** Wrap "an action + its receiver + its parameters" in an object with an `execute()` method (and often `undo()`).
- **Problem it solves:** The invoker (button, scheduler, menu) is hard-wired to the receiver's methods; no way to queue, log, or undo actions.
- **Use when:** Undo/redo, task queues, macro recording, transactional operations, remote/delayed execution.
- **Avoid when:** A simple direct method call works and you need none of: undo, queuing, logging, or decoupling.

## Key Takeaways
- Decouples **who asks** (invoker) from **who does** (receiver).
- Commands are objects, so they can be **stored, queued, logged, serialized, retried**.
- **Undo** = each command stores enough state to reverse itself; the invoker keeps a history stack.
- In Java 8+, a simple command is just a `Runnable` or lambda.
- JDK examples: `Runnable`/`Callable` submitted to an `ExecutorService`; Swing `Action`.

## Pattern Profile
- **Participants:**
  - `Command` (`Command`) — `execute()` / `undo()`.
  - `ConcreteCommand` (`AddTextCommand`) — binds a receiver + params.
  - `Receiver` (`TextEditor`) — performs the actual work.
  - `Invoker` (`EditorInvoker`) — triggers commands, keeps history.
  - `Client` — creates commands and hands them to the invoker.
- **Real-world use:** Text editor undo/redo, job queues, smart-home remote buttons, DB migration steps, restaurant order tickets.
- **Before:** A button's click handler calls `editor.append(...)` directly; undo would require tracking every possible action manually.
- **After:** Every action is a command with its own undo; the invoker just pushes/pops a stack.
- **Trade-off:** A class (or lambda) per action; undo needs careful state capture.
- **vs. [Strategy](Strategy.md):** Strategy = *how* to do one task (interchangeable algorithms). Command = *what* to do (a request object), with lifecycle features like queuing and undo.

## Java Example
```java
import java.util.ArrayDeque;
import java.util.Deque;

class TextEditor {                                        // Receiver
    private final StringBuilder text = new StringBuilder();
    void append(String s)  { text.append(s); }
    void delete(int count) { text.setLength(text.length() - count); }
    String getText()       { return text.toString(); }
}

interface Command {                                       // Command
    void execute();
    void undo();
}

class AddTextCommand implements Command {                 // ConcreteCommand
    private final TextEditor editor;
    private final String toAdd;
    AddTextCommand(TextEditor editor, String toAdd) { this.editor = editor; this.toAdd = toAdd; }

    public void execute() { editor.append(toAdd); }
    public void undo()    { editor.delete(toAdd.length()); }   // knows how to reverse itself
}

class EditorInvoker {                                     // Invoker
    private final Deque<Command> history = new ArrayDeque<>();

    void run(Command c) { c.execute(); history.push(c); }
    void undo() { if (!history.isEmpty()) history.pop().undo(); }
}

class Main {
    public static void main(String[] args) {
        TextEditor editor = new TextEditor();
        EditorInvoker invoker = new EditorInvoker();

        invoker.run(new AddTextCommand(editor, "Hello"));
        invoker.run(new AddTextCommand(editor, " World"));
        System.out.println(editor.getText());   // Hello World

        invoker.undo();
        System.out.println(editor.getText());   // Hello
    }
}
```

## Interview Questions
**Q1. How do you implement undo/redo with Command?** *(most common)*
Each command implements `undo()`. The invoker pushes executed commands onto an undo stack; undo pops and calls `undo()` and pushes the command onto a redo stack. A new command clears the redo stack.

**Q2. Command vs Strategy?**
Strategy encapsulates an algorithm the context uses to do its job. Command encapsulates a complete request (action + receiver + args) that can be queued, logged, or undone.

**Q3. Command vs Memento for undo?**
Command undoes by performing the inverse operation (cheap, but needs reversible logic). Memento undoes by restoring a saved snapshot (simple, but memory-heavy). They're often combined.

**Q4. How does Java's `ExecutorService` relate to Command?**
`Runnable`/`Callable` are commands; the executor is the invoker that queues and runs them on threads.

**Q5. What is a macro command?**
A composite command holding a list of commands. `execute()` runs them all; `undo()` reverses them in reverse order.

**Q6. Where does the receiver come from?**
The client binds it when constructing the command. The invoker never sees it.

## Common Pitfalls
- Putting business logic inside the command instead of the receiver.
- Implementing undo without capturing the state needed to reverse (e.g., what was deleted).
- Forgetting to clear the redo stack after a new command.

## Related Topics
- [Memento](Memento.md)
- [Strategy](Strategy.md)
- [Chain of Responsibility](Chain-of-Responsibility.md)
- [Composite](../Structural/Composite.md) — macro commands

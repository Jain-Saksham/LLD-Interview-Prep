# Interpreter

> **Category:** Behavioral
> **Intent:** Given a language, define a representation for its grammar along with an interpreter that evaluates sentences in that language.

## Quick Summary
- **Definition:** Each grammar rule becomes a class; sentences are represented as a tree of these classes (an AST), and `interpret()` evaluates it recursively.
- **Problem it solves:** Repeatedly evaluating expressions of a small, well-defined language (rules, filters, formulas) without hand-written parsing logic sprinkled everywhere.
- **Use when:** The grammar is **simple and stable**: boolean rule engines, simple math expressions, search filters.
- **Avoid when:** The grammar is complex or performance matters. Use a parser generator (ANTLR) or an existing expression library.

## Key Takeaways
- **Terminal expressions** (numbers, variables) are leaves; **non-terminal expressions** (`Add`, `And`) combine sub-expressions. Structurally a [Composite](../Structural/Composite.md).
- A **context** carries variable values or input during evaluation.
- The pattern covers **evaluation**, not parsing. Building the tree (parsing) is a separate concern.
- One class per rule, so it scales poorly for large grammars.
- Real-world: `java.util.regex.Pattern`, SpEL, SQL `WHERE` engines, rule engines.

## Pattern Profile
- **Participants:**
  - `AbstractExpression` (`Expression`) — declares `interpret(context)`.
  - `TerminalExpression` (`Constant`, `Variable`) — leaf rules.
  - `NonterminalExpression` (`Add`, `Multiply`) — composite rules.
  - `Context` (`Map<String, Integer>`) — external info (variable values).
- **Real-world use:** Discount rule DSLs ("cart > 1000 AND user is premium"), calculator apps, feature-flag targeting rules.
- **Before:** Ad-hoc string parsing and nested `if`s to evaluate each rule.
- **After:** Rules are composable expression objects evaluated uniformly.
- **Trade-off:** Class explosion and poor performance for non-trivial grammars.
- **vs. [Composite](../Structural/Composite.md):** Interpreter *is* a Composite whose purpose is evaluating a language. Composite is the general part-whole structure. Also vs [Visitor](Visitor.md): Visitor lets you add many operations (print, evaluate, optimize) over the same expression tree.

## Java Example
```java
import java.util.Map;

interface Expression {                                     // AbstractExpression
    int interpret(Map<String, Integer> context);
}

class Constant implements Expression {                       // TerminalExpression
    private final int value;
    Constant(int value) { this.value = value; }
    public int interpret(Map<String, Integer> ctx) { return value; }
}

class Variable implements Expression {                     // TerminalExpression
    private final String name;
    Variable(String name) { this.name = name; }
    public int interpret(Map<String, Integer> ctx) { return ctx.getOrDefault(name, 0); }
}

class Add implements Expression {                          // NonterminalExpression
    private final Expression left, right;
    Add(Expression left, Expression right) { this.left = left; this.right = right; }
    public int interpret(Map<String, Integer> ctx) { return left.interpret(ctx) + right.interpret(ctx); }
}

class Multiply implements Expression {                     // NonterminalExpression
    private final Expression left, right;
    Multiply(Expression left, Expression right) { this.left = left; this.right = right; }
    public int interpret(Map<String, Integer> ctx) { return left.interpret(ctx) * right.interpret(ctx); }
}

class Main {
    public static void main(String[] args) {
        // Represents: (x + 5) * y  (tree built by hand; parsing is a separate concern)
        Expression expr = new Multiply(new Add(new Variable("x"), new Constant(5)), new Variable("y"));
        System.out.println(expr.interpret(Map.of("x", 3, "y", 4)));   // (3 + 5) * 4 = 32
    }
}
```

## Interview Questions
**Q1. When would you use Interpreter in a real system?** *(most common)*
For a small, stable DSL evaluated often: pricing/discount rules, alert conditions, feature-flag targeting. For anything complex, use a parser generator or an existing expression engine.

**Q2. Terminal vs non-terminal expressions?**
Terminals are leaves (literals, variables). Non-terminals combine other expressions (operators, `AND`/`OR`).

**Q3. Does the Interpreter pattern include parsing?**
No. It defines how to represent and evaluate the grammar. Converting text into the expression tree is a separate parser.

**Q4. Interpreter vs Composite?**
Interpreter is a specialized Composite: the tree represents a sentence in a grammar and the operation is evaluation.

**Q5. What are its limitations?**
One class per grammar rule, so it's hard to maintain for big grammars, and recursive evaluation can be slow.

## Common Pitfalls
- Using it for complex grammars instead of proper parsing tools.
- Mixing parsing logic into expression classes.
- Not mentioning that it's rarely used directly in day-to-day application code.

## Related Topics
- [Composite](../Structural/Composite.md)
- [Visitor](Visitor.md)
- [Iterator](Iterator.md) — traversing the expression tree

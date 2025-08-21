r[expr.loop]
# Loops and other breakable expressions

r[expr.loop.syntax]
```grammar,expressions
LoopExpression ->
    InfiniteLoopExpression
  | PredicateLoopExpression
```

r[expr.loop.intro]
Rust supports two loop expressions:

*   A [`loop` expression](#infinite-loops) denotes an infinite loop.
*   A [`while` expression](#predicate-loops) loops until a predicate is false.

r[expr.loop.break-label]
All loop types support [`break` expressions](#break-expressions).

r[expr.loop.continue-label]
`loop` and `while` expressions support [`continue` expressions](#continue-expressions).

r[expr.loop.explicit-result]
Only `loop` expressions support [evaluation to non-trivial values](#break-and-loop-values).

r[expr.loop.infinite]
## Infinite loops

r[expr.loop.infinite.syntax]
```grammar,expressions
InfiniteLoopExpression -> `loop` BlockExpression
```

r[expr.loop.infinite.intro]
A `loop` expression repeats execution of its body continuously:
`loop { println!("I live."); }`.

r[expr.loop.infinite.diverging]
A `loop` expression without an associated `break` expression is diverging and has type [`!`](../types/never.md), which is considered UB.

r[expr.loop.infinite.break]
A `loop` expression containing associated [`break` expression(s)](#break-expressions) may terminate, and must have type compatible with the value of the `break` expression(s).

r[expr.loop.while]
## Predicate loops

r[expr.loop.while.grammar]
```grammar,expressions
PredicateLoopExpression -> `while` Conditions BlockExpression
```

r[expr.loop.while.intro]
A `while` loop expression allows repeating the evaluation of a block while a set of conditions remain true.

r[expr.loop.while.syntax]
The syntax of a `while` expression is a condition expression enclosed in parentheses,
followed by a [BlockExpression].
Within the parentheses, condition operands can be separated by `&&` to form chains.

r[expr.loop.while.condition]
Condition operands must be an [Expression] with a [boolean type].
If all of the condition operands evaluate to `true`,
then the loop body block executes.

r[expr.loop.while.repeat]
After the loop body successfully executes, the condition operands are re-evaluated to determine if the body should be executed again.

r[expr.loop.while.exit]
If any condition operand evaluates to `false`,
the body is not executed and execution continues after the `while` expression.

r[expr.loop.while.short-circuit]
Short-circuit evaluation must be supported in `while` loops.

r[expr.loop.while.eval]
A `while` expression evaluates to `()`.

An example:

```rust
let mut i = 0;

while (i < 10) {
    println!("hello");
    i = i + 1;
}
```

r[expr.loop.break]
## `break` expressions

r[expr.loop.break.syntax]
```grammar,expressions
BreakExpression -> `break` Expression?
```

r[expr.loop.break.intro]
When `break` is encountered, execution of the associated loop body is immediately terminated, for example:

```rust
let mut last = 0;
let mut x = 1;
while (x < 100) {
    if (x > 12) {
        break;
    }
    last = x;
    x += 1;
}
assert_eq!(last, 12);
```

r[expr.loop.break.label]
A `break` expression is normally associated with the innermost `loop` or `while` loop enclosing the `break` expression.

r[expr.loop.break.value]
A `break` expression is only permitted in the body of a loop, and has one of the forms `break` or ([see below](#break-and-loop-values)) `break EXPR`.

r[expr.loop.continue]
## `continue` expressions

r[expr.loop.continue.syntax]
```grammar,expressions
ContinueExpression -> `continue`
```

r[expr.loop.continue.intro]
When `continue` is encountered, the current iteration of the associated loop body is immediately terminated, returning control to the loop *head*.

r[expr.loop.continue.while]
In the case of a `while` loop, the head is the conditional operands controlling the loop.

r[expr.loop.continue.label]
`continue` is associated with the innermost enclosing loop.

r[expr.loop.continue.in-loop-only]
A `continue` expression is only permitted in the body of a loop.

r[expr.loop.break-value]
## `break` and loop values

r[expr.loop.break-value.intro]
When associated with a `loop`, a break expression may be used to return a value from that loop, via one of the forms `break EXPR` or `break 'label EXPR`, where `EXPR` is an expression whose result is returned from the `loop`.
For example:

```rust
let (mut a, mut b) = (1, 1);
let result = loop {
    if (b > 10) {
        break b;
    }
    let c = a + b;
    a = b;
    b = c;
};
// first number in Fibonacci sequence over 10:
assert_eq!(result, 13);
```

r[expr.loop.break-value.loop]
In the case a `loop` has an associated `break`, it is not considered diverging, and the `loop` must have a type compatible with each `break` expression.
`break` without an expression is considered identical to `break` with expression `()`.

[`if` condition chains]: if-expr.md#chains-of-conditions
[`if` expressions]: if-expr.md
[`match` expression]: match-expr.md
[boolean type]: ../types/boolean.md
[scrutinee]: ../glossary.md#scrutinee
[temporary values]: ../expressions.md#temporaries

<script>
(function() {
    var fragments = {
        "#predicate-pattern-loops": "loop-expr.html#while-let-patterns",
    };
    var target = fragments[window.location.hash];
    if (target) {
        var url = window.location.toString();
        var base = url.substring(0, url.lastIndexOf('/'));
        window.location.replace(base + "/" + target);
    }
})();
</script>

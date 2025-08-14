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
The syntax of a `while` expression is a sequence of one or more condition operands separated by `&&`,
followed by a [BlockExpression].

r[expr.loop.while.condition]
Condition operands must be either an [Expression] with a [boolean type] or a conditional `let` match.
If all of the condition operands evaluate to `true` and all of the `let` patterns successfully match their [scrutinee]s,
then the loop body block executes.

r[expr.loop.while.repeat]
After the loop body successfully executes, the condition operands are re-evaluated to determine if the body should be executed again.

r[expr.loop.while.exit]
If any condition operand evaluates to `false` or any `let` pattern does not match its scrutinee,
the body is not executed and execution continues after the `while` expression.

r[expr.loop.while.eval]
A `while` expression evaluates to `()`.

An example:

```rust
let mut i = 0;

while i < 10 {
    println!("hello");
    i = i + 1;
}
```

r[expr.loop.while.let]
### `while let` patterns

r[expr.loop.while.let.intro]
`let` patterns in a `while` condition allow binding new variables into scope when the pattern matches successfully.
The following examples illustrate bindings using `let` patterns:

```rust
let mut x = vec![1, 2, 3];

while let Some(y) = x.pop() {
    println!("y = {}", y);
}

while let _ = 5 {
    println!("Irrefutable patterns are always true");
    break;
}
```

r[expr.loop.while.let.desugar]
A `while let` loop is equivalent to a `loop` expression containing a [`match` expression] as follows.

<!-- ignore: expansion example -->
```rust,ignore
'label: while let PATS = EXPR {
    /* loop body */
}
```

is equivalent to

<!-- ignore: expansion example -->
```rust,ignore
'label: loop {
    match EXPR {
        PATS => { /* loop body */ },
        _ => break,
    }
}
```

r[expr.loop.while.let.or-pattern]
Multiple patterns may be specified with the `|` operator.
This has the same semantics as with `|` in `match` expressions:

```rust
let mut vals = vec![2, 3, 1, 2, 2];
while let Some(v @ 1) | Some(v @ 2) = vals.pop() {
    // Prints 2, 2, then 1
    println!("{}", v);
}
```

r[expr.loop.while.chains]
### `while` condition chains

r[expr.loop.while.chains.intro]
Multiple condition operands can be separated with `&&`.
These have the same semantics and restrictions as [`if` condition chains].

The following is an example of chaining multiple expressions, mixing `let` bindings and boolean expressions, and with expressions able to reference pattern bindings from previous expressions:

```rust
fn main() {
    let outer_opt = Some(Some(1i32));

    while let Some(inner_opt) = outer_opt
        && let Some(number) = inner_opt
        && number == 1
    {
        println!("Peek a boo");
        break;
    }
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
while x < 100 {
    if x > 12 {
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
    if b > 10 {
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

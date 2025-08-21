r[expr.if]
# `if` expressions

r[expr.if.syntax]
```grammar,expressions
IfExpression ->
    `if` Conditions BlockExpression
    (`else` ( BlockExpression | IfExpression ) )?

Conditions ->
      `(` Expression _except [StructExpression]_ `)`
```
<!-- TODO: The struct exception above needs clarification, see https://github.com/rust-lang/reference/issues/1808
     The chain grammar could use some work, see https://github.com/rust-lang/reference/issues/1811
-->

r[expr.if.intro]
The syntax of an `if` expression is a sequence of one or more condition operands separated by `&&`,
followed by a consequent block, any number of `else if` conditions and blocks, and an optional trailing `else` block.

r[expr.if.condition]
Condition operands must be an [Expression] with a [boolean type].

r[expr.if.condition-true]
If all of the condition operands evaluate to `true`, 
the consequent block is executed and any subsequent `else if` or `else` block is skipped.

r[expr.if.else-if]
If any condition operand evaluates to `false`, 
the consequent block is skipped and any subsequent `else if` condition is evaluated.

r[expr.if.else]
If all `if` and `else if` conditions evaluate to `false` then any `else` block is executed.

r[expr.if.result]
An `if` expression evaluates to the same value as the executed block, or `()` if no block is evaluated.

r[expr.if.type]
An `if` expression must have the same type in all situations.

```rust
# let x = 3;
if (x == 4) {
    println!("x is four");
} else if (x == 3) {
    println!("x is three");
} else {
    println!("x is something else");
}

// `if` can be used as an expression.
let y = if (12 * 15 > 150) {
    "Bigger"
} else {
    "Smaller"
};
assert_eq!(y, "Bigger");
```

[`match` expressions]: match-expr.md
[boolean type]: ../types/boolean.md
[scrutinee]: ../glossary.md#scrutinee

<script>
(function() {
    var fragments = {
        "#if-let-expressions": "if-expr.html#if-let-patterns",
    };
    var target = fragments[window.location.hash];
    if (target) {
        var url = window.location.toString();
        var base = url.substring(0, url.lastIndexOf('/'));
        window.location.replace(base + "/" + target);
    }
})();
</script>

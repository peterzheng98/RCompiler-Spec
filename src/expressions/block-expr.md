r[expr.block]
# Block expressions

r[expr.block.syntax]
```grammar,expressions
BlockExpression ->
    `{`
        Statements?
    `}`

Statements ->
      Statement+
    | Statement+ ExpressionWithoutBlock
    | ExpressionWithoutBlock
```

r[expr.block.intro]
A *block expression*, or *block*, is a control flow expression and anonymous namespace scope for items and variable declarations.

r[expr.block.sequential-evaluation]
As a control flow expression, a block sequentially executes its component non-item declaration statements and then its final optional expression.

r[expr.block.namespace]
As an anonymous namespace scope, item declarations are only in scope inside the block itself and variables declared by `let` statements are in scope from the next statement until the end of the block.
See the [scopes] chapter for more details.

r[expr.block.inner-attributes]
The syntax for a block is `{`, then any number of [statements], then an optional expression, called the final operand, and finally a `}`.

r[expr.block.statements]
Statements are usually required to be followed by a semicolon, with two exceptions:

1. Item declaration statements do not need to be followed by a semicolon.
2. Expression statements usually require a following semicolon except if its outer expression is a flow control expression.

r[expr.block.null-statement]
Furthermore, extra semicolons between statements are allowed, but these semicolons do not affect semantics.

r[expr.block.evaluation]
When evaluating a block expression, each statement, except for item declaration statements, is executed sequentially.

r[expr.block.result]
Then the final operand is executed, if given.

r[expr.block.type]
The type of a block is the type of the final operand, or `()` if the final operand is omitted.

```rust
# fn fn_call() {}
let _: () = {
    fn_call();
};

let five: i32 = {
    fn_call();
    5
};

assert_eq!(5, five);
```

> [!NOTE]
> As a control flow expression, if a block expression is the outer expression of an expression statement, the expected type is `()` unless it is followed immediately by a semicolon.

r[expr.block.value]
Blocks are always [value expressions] and evaluate the last operand in value expression context.

> [!NOTE]
> This can be used to force moving a value if really needed. For example, the following example fails on the call to `consume_self` because the struct was moved out of `s` in the block expression.
>
> ```rust,compile_fail
> struct Struct;
>
> impl Struct {
>     fn consume_self(self) {}
>     fn borrow_self(&self) {}
> }
>
> fn move_by_block_expression() {
>     let s = Struct;
>
>     // Move the value out of `s` in the block expression.
>     (&{ s }).borrow_self();
>
>     // Fails to execute because `s` is moved out of.
>     s.consume_self();
> }
> ```

[`await` expressions]: await-expr.md
[`cfg`]: ../conditional-compilation.md
[`for`]: loop-expr.md#iterator-loops
[`loop`]: loop-expr.md#infinite-loops
[`unsafe` blocks]: ../unsafe-keyword.md#unsafe-blocks-unsafe-
[`while`]: loop-expr.md#predicate-loops
[array expressions]: array-expr.md
[call expressions]: call-expr.md
[capture modes]: ../types/closure.md#capture-modes
[constant items]: ../items/constant-items.md
[free item]: ../glossary.md#free-item
[function]: ../items/functions.md
[inner attributes]: ../attributes.md
[method]: ../items/associated-items.md#methods
[mutable reference]: ../types/pointer.md#mutables-references-
[scopes]: ../names/scopes.md
[shared references]: ../types/pointer.md#shared-references-
[statement]: ../statements.md
[statements]: ../statements.md
[struct]: struct-expr.md
[the lint check attributes]: ../attributes/diagnostics.md#lint-check-attributes
[tuple expressions]: tuple-expr.md
[unsafe operations]: ../unsafety.md
[value expressions]: ../expressions.md#place-expressions-and-value-expressions
[Loops and other breakable expressions]: loop-expr.md#labelled-block-expressions

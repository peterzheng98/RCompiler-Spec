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

r[expr.block.const]
## `const` blocks

r[expr.block.const.syntax]
```grammar,expressions
ConstBlockExpression -> `const` BlockExpression
```

r[expr.block.const.intro]
A *const block* is a variant of a block expression whose body evaluates at compile-time instead of at runtime.

r[expr.block.const.context]
Const blocks allows you to define a constant value without having to define new [constant items], and thus they are also sometimes referred as *inline consts*.
It also supports type inference so there is no need to specify the type, unlike [constant items].

r[expr.block.const.generic-params]
Const blocks have the ability to reference generic parameters in scope, unlike [free][free item] constant items.
They are desugared to constant items with generic parameters in scope (similar to associated constants, but without a trait or type they are associated with).
For example, this code:

```rust
fn foo<T>() -> usize {
    const { std::mem::size_of::<T>() + 1 }
}
```

is equivalent to:

```rust
fn foo<T>() -> usize {
    {
        struct Const<T>(T);
        impl<T> Const<T> {
            const CONST: usize = std::mem::size_of::<T>() + 1;
        }
        Const::<T>::CONST
    }
}
```

r[expr.block.const.evaluation]

If the const block expression is executed at runtime, then the constant is guaranteed to be evaluated, even if its return value is ignored:

```rust
fn foo<T>() -> usize {
    // If this code ever gets executed, then the assertion has definitely
    // been evaluated at compile-time.
    const { assert!(std::mem::size_of::<T>() > 0); }
    // Here we can have unsafe code relying on the type being non-zero-sized.
    /* ... */
    42
}
```

r[expr.block.const.not-executed]

If the const block expression is not executed at runtime, it may or may not be evaluated:
```rust,compile_fail
if (false) {
    // The panic may or may not occur when the program is built.
    const { panic!(); }
}
```

r[expr.block.label]
## Labelled block expressions

Labelled block expressions are documented in the [Loops and other breakable expressions] section.

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

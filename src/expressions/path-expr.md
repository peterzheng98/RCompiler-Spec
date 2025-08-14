r[expr.path]
# Path expressions

r[expr.path.syntax]
```grammar,expressions
PathExpression ->
      PathInExpression
```

r[expr.path.intro]
A [path] used as an expression context denotes either a local variable or an item.

r[expr.path.place]
Path expressions that resolve to local or static variables are [place expressions], other paths are [value expressions].

```rust
# mod globals {
#     pub static STATIC_VAR: i32 = 5;
#     pub static mut STATIC_MUT_VAR: i32 = 7;
# }
let local_var = 3;
local_var;
```

r[expr.path.const]
Evaluation of associated constants is handled the same way as [`const` blocks].

[place expressions]: ../expressions.md#place-expressions-and-value-expressions
[value expressions]: ../expressions.md#place-expressions-and-value-expressions
[path]: ../paths.md
[`static mut`]: ../items/static-items.md#mutable-statics
[`unsafe` block]: block-expr.md#unsafe-blocks
[`const` blocks]: block-expr.md#const-blocks

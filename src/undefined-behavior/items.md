r[ub.items]
# Undefined behavior list (Items)

r[ub.items.structs]
## Structs

Tuple structs are not supported in this specification. The tuple-struct item form `struct Name(T1, T2, ...)` is invalid and outside the language. Any examples or rules referring to tuple struct items do not apply; only named-field structs and tuple-like enum variants are supported.

r[ub.items.const-item]
## Constant items

There are no borrows (except `&str`), unnamed constants, const functions, or [ExpressionWithBlock]s in const expressions.

r[ub.items.const-eval]
## Const evaluation

We ensure that all usage of other constant items is defined before they are used. As a result, it is not necessary to build a dependency graph of constant items to evaluate them in the correct order. Scanning the AST suffices to evaluate all constant items.

The following situations are not considered:
```rust
const SQR: i32 = N * N;
const N: i32 = A + 8;
const A: i32 = 5;
```

Further,
```rust
struct B {
    a: usize,
    b: [i32; A.a], // error
}
const A: B = B { a: 3, b: [1, 2, 3] }; 
```

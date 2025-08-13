r[type.inferred]
# Inferred type

r[type.inferred.syntax]
```grammar,types
InferredType -> `_`
```

r[type.inferred.intro]
The inferred type asks the compiler to infer the type if possible based on the
surrounding information available.

r[type.inferred.constraint]
It cannot be used in item signatures. For example, 

```rust
fn f(x: _) -> usize { x + 1 }
```

is not valid.

It is often used in generic arguments in rust:

```rust
let x: Vec<_> = (0..10).collect();
```

But we neither have generic parameters nor a `Vec`.

<!--
  What else should be said here?
  The only documentation I am aware of is https://rustc-dev-guide.rust-lang.org/type-inference.html
  There should be a broader discussion of type inference somewhere.
-->

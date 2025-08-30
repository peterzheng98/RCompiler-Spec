r[type.enum]
# Enumerated types

r[type.enum.intro]
An *enumerated type* is a nominal, heterogeneous disjoint union type, denoted
by the name of an [`enum` item]. [^enumtype]

r[type.enum.declaration]
An [`enum` item] declares both the type and a number of *variants*, each of
which is independently named and has the syntax of a struct, tuple struct or
unit-like struct.

r[type.enum.constructor]
New instances of an `enum` can be constructed with a [struct expression].

r[type.enum.value]
Any `enum` value consumes as much memory as the largest variant for its
corresponding `enum` type, as well as the size needed to store a discriminant.

r[type.enum.name]
Enum types cannot be denoted *structurally* as types, but must be denoted by
named reference to an [`enum` item]. For example,

```rust
fn g(e: enum { A, B }) { }
```

is not supported, which should be written as:

```rust
enum E { A, B }
fn g(e: E) { }
```

r[type.enum.comparison]
Variants in the same `enum` can be compared for equality `==` and inequality `!=`.

[^enumtype]: The `enum` type is analogous to a `data` constructor declaration in
             Haskell, or a *pick ADT* in Limbo.

[`enum` item]: ../items/enumerations.md
[struct expression]: ../expressions/struct-expr.md

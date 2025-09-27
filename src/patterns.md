r[patterns]
# Patterns

r[patterns.syntax]
```grammar,patterns
PatternNoTopAlt ->
      PatternWithoutRange

PatternWithoutRange ->
      IdentifierPattern
    | ReferencePattern
```

r[patterns.intro]
Patterns are used to match values against structures and to, optionally, bind variables to values inside these structures.
They are also used in variable declarations and parameters for functions.


r[patterns.usage]
Patterns are used in:

r[patterns.let]
* [`let` declarations](statements.md#let-statements)

r[patterns.param]
* [Function](items/functions.md) parameters

r[patterns.refutable]
## Refutability

A pattern is said to be *refutable* when it has the possibility of not being matched by the value it is being matched against.
*Irrefutable* patterns, on the other hand, always match the value they are being matched against.
Examples:

```rust
let (x, y) = (1, 2);               // "(x, y)" is an irrefutable pattern

if (let (a, 3) = (1, 2)) {           // "(a, 3)" is refutable, and will not match
    panic!("Shouldn't reach here");
} else if (let (a, 4) = (3, 4)) {    // "(a, 4)" is refutable, and will match
    println!("Matched ({}, 4)", a);
}
```

r[patterns.ident]
## Identifier patterns

r[patterns.ident.syntax]
```grammar,patterns
IdentifierPattern -> `ref`? `mut`? IDENTIFIER
```

r[patterns.ident.intro]
Identifier patterns bind the value they match to a variable in the [value namespace].

r[patterns.ident.unique]
The identifier must be unique within the pattern.

r[patterns.ident.scope]
The variable will shadow any variables of the same name in [scope].

r[patterns.ident.bare]
Patterns that consist of only an identifier, possibly with a `mut`, match any value and bind it to that identifier.
This is the most commonly used pattern in variable declarations and parameters for functions and closures.



r[patterns.ident.move]
By default, identifier patterns bind a variable to a copy of or pass a reference from the matched value depending on whether the matched value implements [`Copy`]. So for user-defined structs, the binding will pass a reference.

```rust
struct Foo {
    x: i32,
    y: i32,
}
let mut d: Foo = Foo { x: 1, y: 2 };
let e: Foo = d; // equivalent to `let ref e: Foo = d;`
```

r[patterns.ident.ref]
We also allow binding to a reference by using the `ref` keyword, or to a mutable reference using `ref mut`.

r[patterns.ident.ref-ignored]
`ref` is not something that is being matched against.
Its objective is exclusively to make the matched binding a reference, instead of potentially copying or moving what was matched.

r[patterns.ident.constraint]
It is an error if `ref` or `ref mut` is specified and the identifier shadows a constant.

r[patterns.ref]
## Reference patterns

r[patterns.ref.syntax]
```grammar,patterns
ReferencePattern -> (`&`|`&&`) `mut`? PatternWithoutRange
```

r[patterns.ref.intro]
Reference patterns dereference the pointers that are being matched and, thus, borrow them.

For example, these two matches on `x: &i32` are equivalent:

```rust
let int_reference = &3;

let a = match *int_reference { 0 => "zero", _ => "some" };
let b = match int_reference { &0 => "zero", _ => "some" };

assert_eq!(a, b);
```

r[patterns.ref.ref-ref]
The grammar production for reference patterns has to match the token `&&` to match a reference to a reference because it is a token by itself, not two `&` tokens.

r[patterns.ref.mut]
Adding the `mut` keyword dereferences a mutable reference. The mutability must match the mutability of the reference.

r[patterns.ref.refutable]
Reference patterns are always irrefutable.

[PR #138458]: https://github.com/rust-lang/rust/pull/138458
[PR #140593]: https://github.com/rust-lang/rust/pull/140593#issuecomment-2972338457
[`Copy`]: special-types-and-traits.md#copy
[constant]: items/constant-items.md
[enums]: items/enumerations.md
[literals]: expressions/literal-expr.md
[literal expression]: expressions/literal-expr.md
[negating]: expressions/operator-expr.md#negation-operators
[path]: expressions/path-expr.md
[pattern matching on unions]: items/unions.md#pattern-matching-on-unions
[range expressions]: expressions/range-expr.md
[scope]: names/scopes.md
[structs]: items/structs.md
[tuples]: types/tuple.md
[scrutinee]: glossary.md#scrutinee
[tuple-like enum variant]: items.enum.tuple-expr
[type coercions]: type-coercions.md
[type namespace]: names.namespaces.kinds
[value namespace]: names.namespaces.kinds

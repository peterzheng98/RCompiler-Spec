r[patterns]
# Patterns

r[patterns.syntax]
```grammar,patterns
PatternNoTopAlt ->
      PatternWithoutRange

PatternWithoutRange ->
      LiteralPattern
    | IdentifierPattern
    | WildcardPattern
    | ReferencePattern
    | PathPattern
```

r[patterns.syntax.note]
> NOTE: Compared to Rust, this specification removes `TupleStructPattern` (tuple struct patterns are not supported). Tuple types themselves and their tuple patterns are still supported.

r[patterns.intro]
Patterns are used to match values against structures and to, optionally, bind variables to values inside these structures.
They are also used in variable declarations and parameters for functions and closures.

The pattern in the following example does four things:

* Tests if `person` has the `car` field filled with something.
* Tests if the person's `age` field is between 13 and 19, and binds its value to the `person_age` variable.
* Binds a reference to the `name` field to the variable `person_name`.
* Ignores the rest of the fields of `person`.
  The remaining fields can have any value and are not bound to any variables.

```rust
# struct Car;
# struct Computer;
# struct Person {
#     name: String,
#     car: Option<Car>,
#     computer: Option<Computer>,
#     age: u8,
# }
# let person = Person {
#     name: String::from("John"),
#     car: Some(Car),
#     computer: None,
#     age: 15,
# };
if (let
    Person {
        car: Some(_),
        age: person_age @ 13..=19,
        name: ref person_name,
        ..
    } = person)
{
    println!("{} has a car and is {} years old.", person_name, person_age);
}
```

r[patterns.usage]
Patterns are used in:

r[patterns.let]
* [`let` declarations](statements.md#let-statements)

r[patterns.param]
* [Function](items/functions.md) parameters

r[patterns.destructure]
## Destructuring

r[patterns.destructure.intro]
Patterns can be used to *destructure* [structs], [enums], and [tuples].
Destructuring breaks up a value into its component pieces.
The syntax used is almost the same as when creating such values.

r[patterns.destructure.wildcard]
In a pattern whose [scrutinee] expression has a `struct`, `enum` or `tuple` type, a [wildcard pattern](#wildcard-pattern) (`_`) stands in for a *single* data field, whereas an [et cetera](#grammar-StructPatternEtCetera) or [rest pattern](#rest-patterns) (`..`) stands in for *all* the remaining fields of a particular variant.

r[patterns.destructure.named-field-shorthand]
When destructuring a data structure with named (but not numbered) fields, it is allowed to write `fieldname` as a shorthand for `fieldname: fieldname`.

```rust
# enum Message {
#     Quit,
#     WriteString(String),
#     Move { x: i32, y: i32 },
#     ChangeColor(u8, u8, u8),
# }
# let message = Message::Quit;
match message {
    Message::Quit => println!("Quit"),
    Message::WriteString(write) => println!("{}", &write),
    Message::Move{ x, y: 0 } => println!("move {} horizontally", x),
    Message::Move{ .. } => println!("other move"),
    Message::ChangeColor { 0: red, 1: green, 2: _ } => {
        println!("color change, red: {}, green: {}", red, green);
    }
};
```

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

r[patterns.literal]
## Literal patterns

r[patterns.literal.syntax]
```grammar,patterns
LiteralPattern -> `-`? LiteralExpression
```

r[patterns.literal.intro]
_Literal patterns_ match exactly the same value as what is created by the literal. Since negative numbers are not [literals], literals in patterns may be prefixed by an optional minus sign, which acts like the negation operator.

> [!WARNING]
> C string and raw C string literals are accepted in literal patterns, but `&CStr` doesn't implement structural equality (`#[derive(Eq, PartialEq)]`) and therefore any such `match` on a `&CStr` will be rejected with a type error.

r[patterns.literal.refutable]
Literal patterns are always refutable.

Examples:

```rust
for i in -2..5 {
    match i {
        -1 => println!("It's minus one"),
        1 => println!("It's a one"),
        2|4 => println!("It's either a two or a four"),
        _ => println!("Matched none of the arms"),
    }
}
```

r[patterns.ident]
## Identifier patterns

r[patterns.ident.syntax]
```grammar,patterns
IdentifierPattern -> `ref`? `mut`? IDENTIFIER ( `@` PatternNoTopAlt )?
```

r[patterns.ident.intro]
Identifier patterns bind the value they match to a variable in the [value namespace].

r[patterns.ident.unique]
The identifier must be unique within the pattern.

r[patterns.ident.scope]
The variable will shadow any variables of the same name in scope.
The [scope] of the new binding depends on the context of where the pattern is used (such as a `let` binding or a `match` arm).

r[patterns.ident.bare]
Patterns that consist of only an identifier, possibly with a `mut`, match any value and bind it to that identifier.
This is the most commonly used pattern in variable declarations and parameters for functions and closures.

```rust
let mut variable = 10;
fn sum(x: i32, y: i32) -> i32 {
#    x + y
# }
```

r[patterns.ident.scrutinized]
To bind the matched value of a pattern to a variable, use the syntax `variable @ subpattern`.
For example, the following binds the value 2 to `e` (not the entire range: the range here is a range subpattern).

```rust
let x = 2;

match x {
    e @ 1 ..= 5 => println!("got a range element {}", e),
    _ => println!("anything"),
}
```

r[patterns.ident.move]
By default, identifier patterns bind a variable to a copy of or move from the matched value depending on whether the matched value implements [`Copy`].

r[patterns.ident.ref]
This can be changed to bind to a reference by using the `ref` keyword, or to a mutable reference using `ref mut`. For example:

```rust
# let a = Some(10);
match a {
    None => (),
    Some(value) => (),
}

match a {
    None => (),
    Some(ref value) => (),
}
```

In the first match expression, the value is copied (or moved).
In the second match, a reference to the same memory location is bound to the variable value.
This syntax is needed because in destructuring subpatterns the `&` operator can't be applied to the value's fields.
For example, the following is not valid:

```rust,compile_fail
# struct Person {
#    name: String,
#    age: u8,
# }
# let value = Person { name: String::from("John"), age: 23 };
if (let Person { name: &person_name, age: 18..=150 } = value) { }
```

To make it valid, write the following:

```rust
# struct Person {
#    name: String,
#    age: u8,
# }
# let value = Person { name: String::from("John"), age: 23 };
if (let Person { name: ref person_name, age: 18..=150 } = value) { }
```

r[patterns.ident.ref-ignored]
Thus, `ref` is not something that is being matched against.
Its objective is exclusively to make the matched binding a reference, instead of potentially copying or moving what was matched.

r[patterns.ident.precedent]
[Path patterns](#path-patterns) take precedence over identifier patterns.

r[patterns.ident.constraint]
It is an error if `ref` or `ref mut` is specified and the identifier shadows a constant.

r[patterns.ident.refutable]
Identifier patterns are irrefutable if the `@` subpattern is irrefutable or the subpattern is not specified.

r[patterns.ident.binding]
### Binding modes

r[patterns.ident.binding.intro]
To service better ergonomics, patterns operate in different *binding modes* in order to make it easier to bind references to values.
When a reference value is matched by a non-reference pattern, it will be automatically treated as a `ref` or `ref mut` binding.
Example:

```rust
let x: &Option<i32> = &Some(3);
if (let Some(y) = x) {
    // y was converted to `ref y` and its type is &i32
}
```

r[patterns.ident.binding.non-reference]
*Non-reference patterns* include all patterns except bindings, [wildcard patterns](#wildcard-pattern) (`_`), [`const` patterns](#path-patterns) of reference types, and [reference patterns](#reference-patterns).

r[patterns.ident.binding.default-mode]
If a binding pattern does not explicitly have `ref`, `ref mut`, or `mut`, then it uses the *default binding mode* to determine how the variable is bound.

r[patterns.ident.binding.move]
The default binding mode starts in "move" mode which uses move semantics.

r[patterns.ident.binding.top-down]
When matching a pattern, the compiler starts from the outside of the pattern and works inwards.

r[patterns.ident.binding.auto-deref]
Each time a reference is matched using a non-reference pattern, it will automatically dereference the value and update the default binding mode.

r[patterns.ident.binding.ref]
References will set the default binding mode to `ref`.

r[patterns.ident.binding.ref-mut]
Mutable references will set the mode to `ref mut` unless the mode is already `ref` in which case it remains `ref`.

r[patterns.ident.binding.nested-references]
If the automatically dereferenced value is still a reference, it is dereferenced and this process repeats.

r[patterns.ident.binding.mode-limitations-binding]
The binding pattern may only explicitly specify a `ref` or `ref mut` binding mode, or specify mutability with `mut`, when the default binding mode is "move". For example, these are not accepted:

```rust,edition2024,compile_fail
let [mut x] = &[()]; //~ ERROR
let [ref x] = &[()]; //~ ERROR
let [ref mut x] = &mut [()]; //~ ERROR
```

r[patterns.ident.binding.mode-limitations.edition2024]
> [!EDITION-2024]
> Before the 2024 edition, bindings could explicitly specify a `ref` or `ref mut` binding mode even when the default binding mode was not "move", and they could specify mutability on such bindings with `mut`. In these editions, specifying `mut` on a binding set the binding mode to "move" regardless of the current default binding mode.

r[patterns.ident.binding.mode-limitations-reference]
Similarly, a reference pattern may only appear when the default binding mode is "move". For example, this is not accepted:

```rust,edition2024,compile_fail
let [&x] = &[&()]; //~ ERROR
```

r[patterns.ident.binding.mode-limitations-reference.edition2024]
> [!EDITION-2024]
> Before the 2024 edition, reference patterns could appear even when the default binding mode was not "move", and had both the effect of matching against the scrutinee and of causing the default binding mode to be reset to "move".

r[patterns.ident.binding.mixed]
Move bindings and reference bindings can be mixed together in the same pattern.
Doing so will result in partial move of the object bound to and the object cannot be used afterwards.
This applies only if the type cannot be copied.

In the example below, `name` is moved out of `person`.
Trying to use `person` as a whole or `person.name` would result in an error because of *partial move*.

Example:

```rust
# struct Person {
#    name: String,
#    age: u8,
# }
# let person = Person{ name: String::from("John"), age: 23 };
// `name` is moved from person and `age` referenced
let Person { name, ref age } = person;
```

r[patterns.wildcard]
## Wildcard pattern

r[patterns.wildcard.syntax]
```grammar,patterns
WildcardPattern -> `_`
```

r[patterns.wildcard.intro]
The _wildcard pattern_ (an underscore symbol) matches any value.
It is used to ignore values when they don't matter.

r[patterns.wildcard.struct-matcher]
Inside other patterns it matches a single data field (as opposed to the `..` which matches the remaining fields).

r[patterns.wildcard.no-binding]
Unlike identifier patterns, it does not copy, move or borrow the value it matches.

Examples:

```rust
# let x = 20;
let (a, _) = (10, x);   // the x is always matched by _
# assert_eq!(a, 10);

// ignore a function/closure param
let real_part = |a: f64, _: f64| { a };

// ignore a field from a struct
# struct RGBA {
#    r: f32,
#    g: f32,
#    b: f32,
#    a: f32,
# }
# let color = RGBA{r: 0.4, g: 0.1, b: 0.9, a: 0.5};
let RGBA{r: red, g: green, b: blue, a: _} = color;
# assert_eq!(color.r, red);
# assert_eq!(color.g, green);
# assert_eq!(color.b, blue);

// accept any Some, with any value
# let x = Some(10);
if (let Some(_) = x) {}
```

r[patterns.wildcard.refutable]
The wildcard pattern is always irrefutable.

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

r[patterns.path]
## Path patterns

r[patterns.path.syntax]
```grammar,patterns
PathPattern -> PathInExpression
```

r[patterns.path.intro]
_Path patterns_ are patterns that refer either to constant values or
to structs or enum variants that have no fields.

r[patterns.path.unqualified]
Unqualified path patterns can refer to:

* enum variants
* structs
* constants
* associated constants

r[patterns.path.qualified]
Qualified path patterns can only refer to associated constants.

r[patterns.path.refutable]
Path patterns are irrefutable when they refer to structs or an enum variant when the enum has only one variant or a constant whose type is irrefutable.
They are refutable when they refer to refutable constants or enum variants for enums with multiple variants.

r[patterns.const]
### Constant patterns

r[patterns.const.partial-eq]
When a constant `C` of type `T` is used as a pattern, we first check that `T: PartialEq`.

r[patterns.const.structural-equality]
Furthermore we require that the value of `C` *has (recursive) structural equality*, which is defined recursively as follows:

r[patterns.const.primitive]
- Integers as well as `str`, `bool` and `char` values always have structural equality.

r[patterns.const.builtin-aggregate]
- Tuples, arrays, and slices have structural equality if all their fields/elements have structural equality.
  (In particular, `()` and `[]` always have structural equality.)

r[patterns.const.ref]
- References have structural equality if the value they point to has structural equality.

r[patterns.const.aggregate]
- A value of `struct` or `enum` type has structural equality if its `PartialEq` instance is derived via `#[derive(PartialEq)]`,
  and all fields (for enums: of the active variant) have structural equality.

r[patterns.const.pointer]
- A raw pointer has structural equality if it was defined as a constant integer (and then cast/transmuted).

r[patterns.const.float]
- A float value has structural equality if it is not a `NaN`.

r[patterns.const.exhaustive]
- Nothing else has structural equality.

r[patterns.const.generic]
In particular, the value of `C` must be known at pattern-building time (which is pre-monomorphization).
This means that associated consts that involve generic parameters cannot be used as patterns.

r[patterns.const.immutable]
The value of `C` must not contain any references to mutable statics (`static mut` items or interior mutable `static` items) or `extern` statics.

r[patterns.const.translation]
After ensuring all conditions are met, the constant value is translated into a pattern, and now behaves exactly as-if that pattern had been written directly.
In particular, it fully participates in exhaustiveness checking.
(For raw pointers, constants are the only way to write such patterns. Only `_` is ever considered exhaustive for these types.)

<!-- r[patterns.or]
## Or-patterns

_Or-patterns_ are patterns that match on one of two or more sub-patterns (for example `A | B | C`).
They can nest arbitrarily.
Syntactically, or-patterns are allowed in any of the places where other patterns are allowed (represented by the [Pattern] production), with the exceptions of `let`-bindings and function and closure arguments (represented by the [PatternNoTopAlt] production).

r[patterns.constraints]
### Static semantics

r[patterns.constraints.pattern]
1. Given a pattern `p | q` at some depth for some arbitrary patterns `p` and `q`, the pattern is considered ill-formed if:

   + the type inferred for `p` does not unify with the type inferred for `q`, or
   + the same set of bindings are not introduced in `p` and `q`, or
   + the type of any two bindings with the same name in `p` and `q` do not unify with respect to types or binding modes.

   Unification of types is in all instances aforementioned exact and implicit [type coercions] do not apply.

r[patterns.constraints.match-type-check]
2. When type checking an expression `match e_s { a_1 => e_1, ... a_n => e_n }`,
   for each match arm `a_i` which contains a pattern of form `p_i | q_i`,
   the pattern `p_i | q_i` is considered ill formed if,
   at the depth `d` where it exists the fragment of `e_s` at depth `d`,
   the type of the expression fragment does not unify with `p_i | q_i`.

r[patterns.constraints.exhaustiveness-or-pattern]
3. With respect to exhaustiveness checking, a pattern `p | q` is considered to cover `p` as well as `q`.
   For some constructor `c(x, ..)` the distributive law applies such that `c(p | q, ..rest)` covers the same set of value as `c(p, ..rest) | c(q, ..rest)` does.
   This can be applied recursively until there are no more nested patterns of form `p | q` other than those that exist at the top level.

   Note that by *"constructor"* we refer to a pattern for any product type.
   This includes enum variants, structs with named fields, arrays, tuples, and slices.

r[patterns.behavior]
### Dynamic semantics

r[patterns.behavior.nested-or-patterns]
1. The dynamic semantics of pattern matching a scrutinee expression `e_s` against a pattern `c(p | q, ..rest)` at depth `d` where `c` is some constructor,
   `p` and `q` are arbitrary patterns,
   and `rest` is optionally any remaining potential factors in `c`,
   is defined as being the same as that of `c(p, ..rest) | c(q, ..rest)`.

r[patterns.precedence]
### Precedence with other undelimited patterns

As shown elsewhere in this chapter, there are several types of patterns that are syntactically undelimited, including identifier patterns, reference patterns, and or-patterns.
Or-patterns always have the lowest-precedence.
This allows us to reserve syntactic space for a possible future type ascription feature and also to reduce ambiguity.
For example, `x @ A(..) | B(..)` will result in an error that `x` is not bound in all patterns.
`&A(x) | B(x)` will result in a type mismatch between `x` in the different subpatterns. -->

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

r[paths]
# Paths

r[paths.intro]
A *path* is a sequence of one or more path segments separated by `::` tokens.
Paths are used to refer to [items] ([function]s, [struct]s, [enumeration]s, constant items and associated items) and values (including local variables and `enum` variables).

r[paths.expr]
## Paths in expressions

r[paths.expr.syntax]
```grammar,paths
PathInExpression ->
    PathExprSegment (`::` PathExprSegment)?

PathExprSegment ->
    PathIdentSegment

PathIdentSegment ->
    IDENTIFIER | `Self` | `self`
```

For enumeration, the format is `<enum>::<variant>` and for struct it is `<struct>::<item>`.

Here are some examples:

```rust
let x = 1;
let y = x; // y and x are PathInExpression's
```

```rust
enum D { A, B, C }

fn main() {
    let x = D::A; // D::A is a PathInExpression
    // the compiler finds the enum `D` in the type namespace
}
```

```rust
fn main() {
    struct S;
    impl S {
        const C: isize = 42;
        fn f() {}
        fn g(&self) {
            Self::f(); // Self::f is a PathInExpression
        }
        fn h(&self) {
            self.g(); // self is a PathInExpression
        }
    }
    S::f(); // S::f is a PathInExpression
    let s = S;
    S::g(&s);
    s.h(); // s is a PathInExpression
    let x = S::C; // S::C is a PathInExpression
}
```


<!-- r[paths.expr.intro]
Paths in expressions allow for paths with generic arguments to be specified. They are
used in various places in [expressions] and [patterns].

> [!WARNING]
> Generic arguments with turbofish syntax (`::<...>`) are not supported in this specification.

r[paths.expr.argument-order]
The order of generic arguments is restricted to lifetime arguments, then type
arguments, then const arguments, then equality constraints.

r[paths.expr.complex-const-params]
Const arguments must be surrounded by braces unless they are a [literal], an [inferred const], or a single segment path. An [inferred const] may not be surrounded by braces.

```rust
mod m {
    pub const C: usize = 1;
}
const C: usize = m::C;
fn f<const N: usize>() -> [u8; N] { [0; N] }

// Generic arguments with turbofish syntax are not supported
```

> [!NOTE]
> In a generic argument list, an [inferred const] is parsed as an [inferred type][InferredType] but then semantically treated as a separate kind of [const generic argument].

r[paths.expr.impl-trait-params]
The synthetic type parameters corresponding to `impl Trait` types are implicit,
and these cannot be explicitly specified. -->

r[paths.type]
## Paths in types

r[paths.type.syntax]
```grammar,paths
TypePath -> TypePathSegment

TypePathSegment -> PathIdentSegment
```

r[paths.type.intro]
Type paths are used within type definitions.

r[paths.qualifiers]
## Path qualifiers

Paths can be denoted with various leading qualifiers to change the meaning of
how it is resolved.

r[paths.qualifiers.method-self]
### `self`

r[paths.qualifiers.self-pat]
In a method body, a path which consists of a single `self` segment resolves to the method's self parameter.

```rust
struct S {
    b: bool,
};
impl S {
    fn baz(self) -> bool {
        self.b
    }
}
# fn main() {}
```

r[paths.qualifiers.type-self]
### `Self`

r[paths.qualifiers.type-self.intro]
`Self`, with a capital "S", is used to refer to the current type being implemented or defined. It may be used in the following situations:

r[paths.qualifiers.type-self.trait]
* In a [trait] definition, it refers to the type implementing the trait.

r[paths.qualifiers.type-self.impl]
* In an [implementation], it refers to the type being implemented.
  When implementing a tuple or unit [struct], it also refers to the constructor in the [value namespace].

r[paths.qualifiers.type-self.type]
* In the definition of a [struct] or an [enumeration], it refers to the type being defined.
  The definition is not allowed to be infinitely recursive (there must be an indirection).

r[paths.qualifiers.type-self.scope]
The scope of `Self` behaves similarly to a generic parameter; see the [`Self` scope] section for more details.

r[paths.qualifiers.type-self.allowed-positions]
`Self` can only be used as the first segment, without a preceding `::`.

r[paths.qualifiers.type-self.no-generics]
The `Self` path cannot include generic arguments.

```rust
trait T {
    const C: i32;
    // `Self` will be whatever type that implements `T`.
    fn new() -> Self;
    // `Self::Item` will be the type alias in the implementation.
    fn f(&self) -> i32;
}
struct S;
impl T for S {
    const C: i32 = 9;
    fn new() -> Self {           // `Self` is the type `S`.
        S
    }
    fn f(&self) -> i32 {
        Self::C                  // `Self::C` is the constant value `9`.
    }
}
    
struct NonEmptyList {
    head: usize,
    // A struct can reference itself (as long as it is not 
    // infinitely recursive).
    tail: Option<Box<Self>>,
}

impl NonEmptyList {
    fn new(head: usize) -> Self {
        NonEmptyList {
            head,
            tail: None,
        }
    }

    fn append(&mut self, value: usize) {
        match self.tail.as_mut() {
            Some(tail) => tail.append(value),
            None => self.tail = Some(Box::new(Self::new(value))),
        }
    }
}
```
<!--
r[paths.qualifiers.crate]
### `crate`

r[paths.qualifiers.crate.intro]
`crate` resolves the path relative to the current crate.

r[paths.qualifiers.crate.allowed-positions]
`crate` can only be used as the first segment, without a preceding `::`.


```rust
const FN: i32 = 1;
fn f() {
    const FN: i32 = 20;
    printlnInt(FN);
}
struct V;
impl V {
    const FN: i32 = 10;
    fn f() {
        printlnInt(Self::FN);
    }
}
trait T{
    const FN: i32 = 30;
    fn f() {
        printlnInt(Self::FN);
    }
}
fn main() {
    struct V;
    trait T{
        const FN: i32 = 300;
        fn f() {
            printlnInt(Self::FN);
        }
    }
    impl crate::T for V {}
    impl T for crate::V {}
    const FN: i32 = 2;
    {
        printlnInt(FN); // 3
        f(); // 20
        {
            f(); // 40
            fn f() {
                printlnInt(40);
            }
            printlnInt(FN); // 4
            const FN: i32 = 4;
            printlnInt(crate::FN); // 1
            crate::f(); // 20
            V::f(); // 30
            crate::V::f(); // 10
        }
        const FN: i32 = 3;
    }
}
```

I suggest not implementing `crate` and letting it shadow.
-->

<!-- 
r[paths.canonical]
## Canonical paths

r[paths.canonical.intro]
Items defined in a module or implementation have a *canonical path* that
corresponds to where within its crate it is defined.

r[paths.canonical.alias]
All other paths to these items are aliases.

r[paths.canonical.def]
The canonical path is defined as a *path prefix* appended by
the path segment the item itself defines.

r[paths.canonical.non-canonical]
[Implementations] and [use declarations] do not have canonical paths, although
the items that implementations define do have them. Items defined in
block expressions do not have canonical paths. Items defined in a module that
does not have a canonical path do not have a canonical path. Associated items
defined in an implementation that refers to an item without a canonical path,
e.g. as the implementing type, the trait being implemented, a type parameter or
bound on a type parameter, do not have canonical paths.

r[paths.canonical.module-prefix]
The path prefix for modules is the canonical path to that module.

r[paths.canonical.bare-impl-prefix]
For bare implementations, it is the canonical path of the item being implemented
surrounded by <span class="parenthetical">angle (`<>`)</span> brackets.

r[paths.canonical.trait-impl-prefix]
For [trait implementations], it is the canonical path of the item being implemented
followed by `as` followed by the canonical path to the trait all surrounded in
<span class="parenthetical">angle (`<>`)</span> brackets.

r[paths.canonical.local-canonical-path]
The canonical path is only meaningful within a given crate. There is no global
namespace across crates; an item's canonical path merely identifies it within
the crate.

```rust
// Comments show the canonical path of the item.

mod a { // crate::a
    pub struct Struct; // crate::a::Struct

    pub trait Trait { // crate::a::Trait
        fn f(&self); // crate::a::Trait::f
    }

    impl Trait for Struct {
        fn f(&self) {} // <crate::a::Struct as crate::a::Trait>::f
    }

    impl Struct {
        fn g(&self) {} // <crate::a::Struct>::g
    }
}

mod without { // crate::without
    fn canonicals() { // crate::without::canonicals
        struct OtherStruct; // None

        trait OtherTrait { // None
            fn g(&self); // None
        }

        impl OtherTrait for OtherStruct {
            fn g(&self) {} // None
        }

        impl OtherTrait for crate::a::Struct {
            fn g(&self) {} // None
        }

        impl crate::a::Trait for OtherStruct {
            fn f(&self) {} // None
        }
    }
}

# fn main() {}
``` -->

[`$crate`]: macro.decl.hygiene.crate
[implementations]: items/implementations.md
[items]: items.md
[literal]: expressions/literal-expr.md
[use declarations]: items/use-declarations.md
[`Self` scope]: names/scopes.md#self-scope
[`use`]: items/use-declarations.md
[attributes]: attributes.md
[const generic argument]: items.generics.const.argument
[enumeration]: items/enumerations.md
[expressions]: expressions.md
[extern prelude]: names/preludes.md#extern-prelude
[implementation]: items/implementations.md
[inferred const]: items.generics.const.inferred
[macro transcribers]: macros-by-example.md
[macros]: macros.md
[mbe]: macros-by-example.md
[patterns]: patterns.md
[struct]: items/structs.md
[trait implementations]: items/implementations.md#trait-implementations
[trait]: items/traits.md
[traits]: items/traits.md
[types]: types.md
[union]: items/unions.md
[value namespace]: names/namespaces.md
[visibility]: visibility-and-privacy.md

r[items.enum]
# Enumerations

r[items.enum.syntax]
```grammar,items
Enumeration ->
    `enum` IDENTIFIER `{` EnumVariants? `}`

EnumVariants -> EnumVariant ( `,` EnumVariant )* `,`?

EnumVariant -> IDENTIFIER
```

r[items.enum.intro]
An *enumeration*, also referred to as an *enum*, is a simultaneous definition of a
nominal [enumerated type] as well as a set of *constructors*, that can be used
to create or pattern-match values of the corresponding enumerated type.

r[items.enum.decl]
Enumerations are declared with the keyword `enum`.

r[items.enum.namespace]
The `enum` declaration defines the enumeration type in the [type namespace] of the block where it is located.

An example of an `enum` item and its use:

```rust
enum Animal {
    Dog,
    Cat,
}

let mut a: Animal = Animal::Dog;
a = Animal::Cat;
```

r[items.enum.unit-only]
If an enum only contains unit variants, the enum is called an
*<span id="unit-only-enum">unit-only enum</span>*. **In RCompiler, all `enum` variants are unit variants.**

r[items.enum.constructor-names]
Variant constructors are similar to [struct] definitions, and can be referenced by a path from the enumeration name.

r[items.enum.constructor-namespace]
Each variant defines its type in the [type namespace], though that type cannot be used as a type specifier.
Unit-like variants also define a constructor in the [value namespace].

r[items.enum.path-expr]
A unit-like variant can be instantiated with a [path expression].
For example:

```rust
enum Examples {
    UnitLike,
}
let x = Examples::UnitLike; // Path expression of the const item.
```
<!-- 
r[items.enum.discriminant]
## Discriminants

r[items.enum.discriminant.intro]
Each enum instance has a _discriminant_: an integer logically associated to it
that is used to determine which variant it holds.

r[items.enum.discriminant.repr-rust]
Under the [`Rust` representation], the discriminant is interpreted as
an `isize` value. However, the compiler is allowed to use a smaller type (or
another means of distinguishing variants) in its actual memory layout.

### Assigning discriminant values

r[items.enum.discriminant.explicit]
#### Explicit discriminants

r[items.enum.discriminant.explicit.intro]
Since our enumerations are "[unit-only]", the discriminant of a variant may be explicitly set by
following the variant name with `=` and a [constant expression]:

r[items.enum.discriminant.implicit]
#### Implicit discriminants

If a discriminant for a variant is not specified, then it is set to one higher
than the discriminant of the previous variant in the declaration. If the
discriminant of the first variant in the declaration is unspecified, then
it is set to zero.

```rust
enum Foo {
    Bar,            // 0
    Baz = 123,      // 123
    Quux,           // 124
}

let baz_discriminant = Foo::Baz as u32;
assert_eq!(baz_discriminant, 123);
```

r[items.enum.discriminant.restrictions]
#### Restrictions

r[items.enum.discriminant.restrictions.same-discriminant]
It is an error when two variants share the same discriminant.

```rust,compile_fail
enum SharedDiscriminantError {
    SharedA = 1,
    SharedB = 1
}

enum SharedDiscriminantError2 {
    Zero,       // 0
    One,        // 1
    OneToo = 1  // 1 (collision with previous!)
}
```

r[items.enum.discriminant.restrictions.overflow-discriminant]
It is also an error to have a discriminant that overflows `isize`, which is checked by your compiler.

### Accessing discriminant

r[items.enum.discriminant.coercion]
#### Casting

r[items.enum.discriminant.coercion.intro]
Discriminants can be directly accessed with a [numeric cast]; e.g.:

```rust
enum Enum {
    Foo,
    Bar,
    Baz,
}

assert_eq!(0, Enum::Foo as isize);
assert_eq!(1, Enum::Bar as isize);
assert_eq!(2, Enum::Baz as isize);
``` -->

[`C` representation]: ../type-layout.md#the-c-representation
[call expression]: ../expressions/call-expr.md
[constant expression]: ../const_eval.md#constant-expressions
[enumerated type]: ../types/enum.md
[Field-less enums]: #field-less-enum
[never type]: ../types/never.md
[numeric cast]: ../expressions/operator-expr.md#semantics
[path expression]: ../expressions/path-expr.md
[primitive representation]: ../type-layout.md#primitive-representations
[`Rust` representation]: ../type-layout.md#the-rust-representation
[struct expression]: ../expressions/struct-expr.md
[struct]: structs.md
[type namespace]: ../names/namespaces.md
[unit-only]: #unit-only-enum
[use declarations]: use-declarations.md
[value namespace]: ../names/namespaces.md

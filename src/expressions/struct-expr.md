r[expr.struct]
# Struct expressions

r[expr.struct.syntax]
```grammar,expressions
StructExpression ->
    ( IDENTIFIER | Self ) `{` (StructExprFields | StructBase)? `}`

StructExprFields ->
    StructExprField (`,` StructExprField)* `,`?

StructExprField ->
        IDENTIFIER
      | IDENTIFIER `:` Expression

```

r[expr.struct.intro]
A *struct expression* creates a struct, enum, or union value.
It consists of a path to a [struct] or [enum variant] item followed by the values for the fields of the item.

The following are examples of struct expressions:

```rust
struct Point { x: f64, y: f64 }
struct NothingInMe { }
struct User { name: String, age: u32, score: usize }
enum Enum { Variant {} }
Point { x: 10.0, y: 20.0 };
NothingInMe {};
let u = User { name: "Joe".to_string(), age: 35, score: 100_000 };
Enum::Variant {};
```

> [!NOTE]
> Tuple structs are not supported in this specification. For tuple-like enum variants, use a [call expression][expr.call] that refers to their constructor in the value namespace; this is distinct from a struct expression with curly braces that refers to the constructor in the type namespace. Unit structs and unit enum variants are typically instantiated using a [path expression][expr.path] that refers to the constant in the value namespace.
>
> ```rust
> struct Gamma;
> // Gamma unit value, referring to the const in the value namespace.
> let a = Gamma;
> // Exact same value as `a`, but constructed using a struct expression
> // referring to the type namespace.
> let b = Gamma {};
>
> enum ColorSpace { Oklch }
> let c = ColorSpace::Oklch;
> let d = ColorSpace::Oklch {};
> ```

r[expr.struct.field]
## Field struct expression

r[expr.struct.field.intro]
A struct expression with fields enclosed in curly braces allows you to specify the value for each individual field in any order.
The field name is separated from its value with a colon.

[enum variant]: ../items/enumerations.md
[if let]: if-expr.md#if-let-patterns
[if]: if-expr.md#if-expressions
[loop]: loop-expr.md
[match]: match-expr.md
[parentheses]: grouped-expr.md
[struct]: ../items/structs.md
[union]: ../items/unions.md
[visible]: ../visibility-and-privacy.md
[scrutinee]: ../glossary.md#scrutinee

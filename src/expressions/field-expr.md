r[expr.field]
# Field access expressions

r[expr.field.syntax]
```grammar,expressions
FieldExpression -> Expression `.` IDENTIFIER
```

r[expr.field.intro]
A *field expression* is a [place expression] that evaluates to the location of a field of a [struct] or [union].

r[expr.field.mut]
When the operand is [mutable], the field expression is also mutable.

r[expr.field.form]
The syntax for a field expression is an expression, called the *container operand*, then a `.`, and finally an [identifier].

r[expr.field.not-method-call]
Field expressions cannot be followed by a parenthetical comma-separated list of expressions, as that is instead parsed as a [method call expression].
That is, they cannot be the function operand of a [call expression].

> [!NOTE]
> Wrap the field expression in a [parenthesized expression] to use it in a call expression.
>
> ```rust
> # struct HoldsCallable<F: Fn()> { callable: F }
> let holds_callable = HoldsCallable { callable: || () };
>
> // Invalid: Parsed as calling the method "callable"
> // holds_callable.callable();
>
> // Valid
> (holds_callable.callable)();
> ```

Examples:

<!-- ignore: needs lots of support code -->
```rust,ignore
mystruct.myfield;
foo().x;
(Struct {a: 10, b: 20}).a;
(mystruct.function_field)() // Call expression containing a field expression
```

r[expr.field.autoref-deref]
## Automatic dereferencing

If the type of the container operand implements [`Deref`] or [`DerefMut`][`Deref`] depending on whether the operand is [mutable], it is *automatically dereferenced* as many times as necessary to make the field access possible.
This process is also called *autoderef* for short.

In RCompiler, only the [`Box`] type is supported for automatic dereferencing.

r[expr.field.borrow]
## Borrowing

The fields of a struct or a reference to a struct are treated as separate entities when borrowing.
If the struct does not implement [`Drop`] and is stored in a local variable, this also applies to moving out of each of its fields.
This also does not apply if automatic dereferencing is done through user-defined types other than [`Box`].

```rust
struct A { f1: String, f2: String, f3: String }
let mut x: A;
# x = A {
#     f1: "f1".to_string(),
#     f2: "f2".to_string(),
#     f3: "f3".to_string()
# };
let a: &mut String = &mut x.f1; // x.f1 borrowed mutably
let b: &String = &x.f2;         // x.f2 borrowed immutably
let c: &String = &x.f2;         // Can borrow again
let d: String = x.f3;           // Move out of x.f3
let e: String = x.f3;           // Error: use of moved value
```

[`Box`]: ../special-types-and-traits.md#boxt
[`Deref`]: ../special-types-and-traits.md#deref-and-derefmut
[`drop`]: ../special-types-and-traits.md#drop
[identifier]: ../identifiers.md
[call expression]: call-expr.md
[method call expression]: method-call-expr.md
[mutable]: ../expressions.md#mutability
[parenthesized expression]: grouped-expr.md
[place expression]: ../expressions.md#place-expressions-and-value-expressions
[struct]: ../items/structs.md
[union]: ../items/unions.md

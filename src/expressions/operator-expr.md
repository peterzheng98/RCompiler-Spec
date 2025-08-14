r[expr.operator]
# Operator expressions

r[expr.operator.syntax]
```grammar,expressions
OperatorExpression ->
      BorrowExpression
    | DereferenceExpression
    | NegationExpression
    | ArithmeticOrLogicalExpression
    | ComparisonExpression
    | LazyBooleanExpression
    | TypeCastExpression
    | AssignmentExpression
    | CompoundAssignmentExpression
```

r[expr.operator.intro]
Operators are defined for built in types by the Rust language.

r[expr.operator.trait]
Many of the following operators can also be overloaded using traits in `std::ops` or `std::cmp`.

r[expr.operator.int-overflow]
## Overflow

r[expr.operator.int-overflow.intro]
Integer operators will panic when they overflow when compiled in debug mode.
The `-C debug-assertions` and `-C overflow-checks` compiler flags can be used to control this more directly.
The following things are considered to be overflow:

r[expr.operator.int-overflow.binary-arith]
* When `+`, `*` or binary `-` create a value greater than the maximum value, or less than the minimum value that can be stored.

r[expr.operator.int-overflow.unary-neg]
* Applying unary `-` to the most negative value of any signed integer type, unless the operand is a [literal expression] (or a literal expression standing alone inside one or more [grouped expressions][grouped expression]).

r[expr.operator.int-overflow.div]
* Using `/` or `%`, where the left-hand argument is the smallest integer of a signed integer type and the right-hand argument is `-1`.
  These checks occur even when `-C overflow-checks` is disabled, for legacy reasons.

r[expr.operator.int-overflow.shift]
* Using `<<` or `>>` where the right-hand argument is greater than or equal to the number of bits in the type of the left-hand argument, or is negative.

> [!NOTE]
> The exception for literal expressions behind unary `-` means that forms such as `-128_i8` or `let j: i8 = -(128)` never cause a panic and have the expected value of -128.
>
> In these cases, the literal expression already has the most negative value for its type (for example, `128_i8` has the value -128) because integer literals are truncated to their type per the description in [Integer literal expressions][literal expression].
>
> Negation of these most negative values leaves the value unchanged due to two's complement overflow conventions.
>
> In `rustc`, these most negative expressions are also ignored by the `overflowing_literals` lint check.

r[expr.operator.borrow]
## Borrow operators

r[expr.operator.borrow.syntax]
```grammar,expressions
BorrowExpression ->
      (`&`|`&&`) Expression
    | (`&`|`&&`) `mut` Expression
```

r[expr.operator.borrow.intro]
The `&` (shared borrow) and `&mut` (mutable borrow) operators are unary prefix operators.

r[expr.operator.borrow.result]
When applied to a [place expression], this expressions produces a reference (pointer) to the location that the value refers to.

r[expr.operator.borrow.lifetime]
The memory location is also placed into a borrowed state for the duration of the reference.
For a shared borrow (`&`), this implies that the place may not be mutated, but it may be read or shared again.
For a mutable borrow (`&mut`), the place may not be accessed in any way until the borrow expires.

r[expr.operator.borrow.mut]
`&mut` evaluates its operand in a mutable place expression context.

r[expr.operator.borrow.temporary]
If the `&` or `&mut` operators are applied to a [value expression], then a [temporary value] is created.

These operators cannot be overloaded.

```rust
{
    // a temporary with value 7 is created that lasts for this scope.
    let shared_reference = &7;
}
let mut array = [-2, 3, 9];
{
    // Mutably borrows `array` for this scope.
    // `array` may only be used through `mutable_reference`.
    let mutable_reference = &mut array;
}
```

r[expr.borrow.and-and-syntax]
Even though `&&` is a single token ([the lazy 'and' operator](#lazy-boolean-operators)), when used in the context of borrow expressions it works as two borrows:

```rust
// same meanings:
let a = &&  10;
let a = & & 10;

// same meanings:
let a = &&&&  mut 10;
let a = && && mut 10;
let a = & & & & mut 10;
```

r[expr.deref]
## The dereference operator

r[expr.deref.syntax]
```grammar,expressions
DereferenceExpression -> `*` Expression
```

r[expr.deref.intro]
The `*` (dereference) operator is also a unary prefix operator.

r[expr.deref.result]
When applied to a [pointer](../types/pointer.md) it denotes the pointed-to location.

r[expr.deref.mut]
If the expression is of type `&mut T` or `*mut T`, and is either a local variable, a (nested) field of a local variable or is a mutable [place expression], then the resulting memory location can be assigned to.

r[expr.deref.safety]
Dereferencing a raw pointer requires `unsafe`.

r[expr.deref.traits]
On non-pointer types `*x` is equivalent to `*std::ops::Deref::deref(&x)` in an [immutable place expression context](../expressions.md#mutability) and `*std::ops::DerefMut::deref_mut(&mut x)` in a mutable place expression context.

```rust
let x = &7;
assert_eq!(*x, 7);
let y = &mut 9;
*y = 11;
assert_eq!(*y, 11);
```

r[expr.negate]
## Negation operators

r[expr.negate.syntax]
```grammar,expressions
NegationExpression ->
      `-` Expression
    | `!` Expression
```

r[expr.negate.intro]
These are the last two unary operators.

r[expr.negate.results]
This table summarizes the behavior of them on primitive types and which traits are used to overload these operators for other types.
Remember that signed integers are always represented using two's complement.
The operands of all of these operators are evaluated in [value expression context][value expression] so are moved or copied.

| Symbol | Integer     | `bool`        | Floating Point | Overloading Trait  |
|--------|-------------|-------------- |----------------|--------------------|
| `-`    | Negation*   |               | Negation       | `std::ops::Neg`    |
| `!`    | Bitwise NOT | [Logical NOT] |                | `std::ops::Not`    |

\* Only for signed integer types.

Here are some example of these operators

```rust
let x = 6;
assert_eq!(-x, -6);
assert_eq!(!x, -7);
assert_eq!(true, !false);
```

r[expr.arith-logic]
## Arithmetic and Logical Binary Operators

r[expr.arith-logic.syntax]
```grammar,expressions
ArithmeticOrLogicalExpression ->
      Expression `+` Expression
    | Expression `-` Expression
    | Expression `*` Expression
    | Expression `/` Expression
    | Expression `%` Expression
    | Expression `&` Expression
    | Expression `|` Expression
    | Expression `^` Expression
    | Expression `<<` Expression
    | Expression `>>` Expression
```

r[expr.arith-logic.intro]
Binary operators expressions are all written with infix notation.

r[expr.arith-logic.behavior]
This table summarizes the behavior of arithmetic and logical binary operators on primitive types and which traits are used to overload these operators for other types.
Remember that signed integers are always represented using two's complement.
The operands of all of these operators are evaluated in [value expression context][value expression] so are moved or copied.

| Symbol | Integer                 | `bool`        | Floating Point | Overloading Trait  | Overloading Compound Assignment Trait |
|--------|-------------------------|---------------|----------------|--------------------| ------------------------------------- |
| `+`    | Addition                |               | Addition       | `std::ops::Add`    | `std::ops::AddAssign`                 |
| `-`    | Subtraction             |               | Subtraction    | `std::ops::Sub`    | `std::ops::SubAssign`                 |
| `*`    | Multiplication          |               | Multiplication | `std::ops::Mul`    | `std::ops::MulAssign`                 |
| `/`    | Division*†              |               | Division       | `std::ops::Div`    | `std::ops::DivAssign`                 |
| `%`    | Remainder**†            |               | Remainder      | `std::ops::Rem`    | `std::ops::RemAssign`                 |
| `&`    | Bitwise AND             | [Logical AND] |                | `std::ops::BitAnd` | `std::ops::BitAndAssign`              |
| <code>&#124;</code> | Bitwise OR | [Logical OR]  |                | `std::ops::BitOr`  | `std::ops::BitOrAssign`               |
| `^`    | Bitwise XOR             | [Logical XOR] |                | `std::ops::BitXor` | `std::ops::BitXorAssign`              |
| `<<`   | Left Shift              |               |                | `std::ops::Shl`    | `std::ops::ShlAssign`                 |
| `>>`   | Right Shift***          |               |                | `std::ops::Shr`    |  `std::ops::ShrAssign`                |

\* Integer division rounds towards zero.

\*\* Rust uses a remainder defined with [truncating division](https://en.wikipedia.org/wiki/Modulo_operation#Variants_of_the_definition). Given `remainder = dividend % divisor`, the remainder will have the same sign as the dividend.

\*\*\* Arithmetic right shift on signed integer types, logical right shift on
unsigned integer types.

† For integer types, division by zero panics.

Here are examples of these operators being used.

```rust
assert_eq!(3 + 6, 9);
assert_eq!(5.5 - 1.25, 4.25);
assert_eq!(-5 * 14, -70);
assert_eq!(14 / 3, 4);
assert_eq!(100 % 7, 2);
assert_eq!(0b1010 & 0b1100, 0b1000);
assert_eq!(0b1010 | 0b1100, 0b1110);
assert_eq!(0b1010 ^ 0b1100, 0b110);
assert_eq!(13 << 3, 104);
assert_eq!(-10 >> 2, -3);
```

r[expr.cmp]
## Comparison Operators

r[expr.cmp.syntax]
```grammar,expressions
ComparisonExpression ->
      Expression `==` Expression
    | Expression `!=` Expression
    | Expression `>` Expression
    | Expression `<` Expression
    | Expression `>=` Expression
    | Expression `<=` Expression
```

r[expr.cmp.intro]
Comparison operators are also defined both for primitive types and many types in the standard library.

r[expr.cmp.paren-chaining]
Parentheses are required when chaining comparison operators. For example, the expression `a == b == c` is invalid and may be written as `(a == b) == c`.

r[expr.cmp.trait]
Unlike arithmetic and logical operators, the traits for overloading these operators are used more generally to show how a type may be compared and will likely be assumed to define actual comparisons by functions that use these traits as bounds.
Many functions and macros in the standard library can then use that assumption (although not to ensure safety).

r[expr.cmp.place]
Unlike the arithmetic and logical operators above, these operators implicitly take shared borrows of their operands, evaluating them in [place expression context][place expression]:

```rust
# let a = 1;
# let b = 1;
a == b;
// is equivalent to
::std::cmp::PartialEq::eq(&a, &b);
```

This means that the operands don't have to be moved out of.

r[expr.cmp.behavior]

| Symbol | Meaning                  | Overloading method         |
|--------|--------------------------|----------------------------|
| `==`   | Equal                    | `std::cmp::PartialEq::eq`  |
| `!=`   | Not equal                | `std::cmp::PartialEq::ne`  |
| `>`    | Greater than             | `std::cmp::PartialOrd::gt` |
| `<`    | Less than                | `std::cmp::PartialOrd::lt` |
| `>=`   | Greater than or equal to | `std::cmp::PartialOrd::ge` |
| `<=`   | Less than or equal to    | `std::cmp::PartialOrd::le` |

Here are examples of the comparison operators being used.

```rust
assert!(123 == 123);
assert!(23 != -12);
assert!(12.5 > 12.2);
assert!([1, 2, 3] < [1, 3, 4]);
assert!('A' <= 'B');
assert!("World" >= "Hello");
```

r[expr.bool-logic]
## Lazy boolean operators

r[expr.bool-logic.syntax]
```grammar,expressions
LazyBooleanExpression ->
      Expression `||` Expression
    | Expression `&&` Expression
```

r[expr.bool-logic.intro]
The operators `||` and `&&` may be applied to operands of boolean type.
The `||` operator denotes logical 'or', and the `&&` operator denotes logical 'and'.

r[expr.bool-logic.conditional-evaluation]
They differ from `|` and `&` in that the right-hand operand is only evaluated when the left-hand operand does not already determine the result of the expression.
That is, `||` only evaluates its right-hand operand when the left-hand operand evaluates to `false`, and `&&` only when it evaluates to `true`.

```rust
let x = false || true; // true
let y = false && panic!(); // false, doesn't evaluate `panic!()`
```

## Assignment expressions

r[expr.assign.syntax]
```grammar,expressions
AssignmentExpression -> Expression `=` Expression
```

r[expr.assign.intro]
An *assignment expression* moves a value into a specified place.

r[expr.assign.assignee]
An assignment expression consists of a [mutable] [assignee expression], the *assignee operand*, followed by an equals sign (`=`) and a [value expression], the *assigned value operand*.

r[expr.assign.behavior-basic]
In its most basic form, an assignee expression is a [place expression], and we discuss this case first.

r[expr.assign.behavior-destructuring]
The more general case of destructuring assignment is discussed below, but this case always decomposes into sequential assignments to place expressions, which may be considered the more fundamental case.

r[expr.assign.basic]
### Basic assignments

r[expr.assign.evaluation-order]
Evaluating assignment expressions begins by evaluating its operands.
The assigned value operand is evaluated first, followed by the assignee expression.

r[expr.assign.destructuring-order]
For destructuring assignment, subexpressions of the assignee expression are evaluated left-to-right.

> [!NOTE]
> This is different than other expressions in that the right operand is evaluated before the left one.

r[expr.assign.drop-target]
It then has the effect of first [dropping] the value at the assigned place, unless the place is an uninitialized local variable or an uninitialized field of a local variable.

r[expr.assign.behavior]
Next it either [copies or moves] the assigned value to the assigned place.

r[expr.assign.result]
An assignment expression always produces [the unit value][unit].

Example:

```rust
let mut x = 0;
let y = 0;
x = y;
```

r[expr.assign.destructure]
### Destructuring assignments

r[expr.assign.destructure.intro]
Destructuring assignment is a counterpart to destructuring pattern matches for variable declaration, permitting assignment to complex values, such as tuples or structs.
For instance, we may swap two mutable variables:

```rust
let (mut a, mut b) = (0, 1);
// Swap `a` and `b` using destructuring assignment.
(b, a) = (a, b);
```

r[expr.assign.destructure.assignee]
In contrast to destructuring declarations using `let`, patterns may not appear on the left-hand side of an assignment due to syntactic ambiguities.
Instead, a group of expressions that correspond to patterns are designated to be [assignee expressions][assignee expression], and permitted on the left-hand side of an assignment.
Assignee expressions are then desugared to pattern matches followed by sequential assignment.

r[expr.assign.destructure.irrefutable]
The desugared patterns must be irrefutable: in particular, this means that only slice patterns whose length is known at compile-time, and the trivial slice `[..]`, are permitted for destructuring assignment.

The desugaring method is straightforward, and is illustrated best by example.

```rust
# struct Struct { x: u32, y: u32 }
# let (mut a, mut b) = (0, 0);
(a, b) = (3, 4);

[a, b] = [3, 4];

Struct { x: a, y: b } = Struct { x: 3, y: 4};

// desugars to:

{
    let (_a, _b) = (3, 4);
    a = _a;
    b = _b;
}

{
    let [_a, _b] = [3, 4];
    a = _a;
    b = _b;
}

{
    let Struct { x: _a, y: _b } = Struct { x: 3, y: 4};
    a = _a;
    b = _b;
}
```

r[expr.assign.destructure.repeat-ident]
Identifiers are not forbidden from being used multiple times in a single assignee expression.

r[expr.assign.destructure.discard-value]
[Underscore expressions] and empty [range expressions] may be used to ignore certain values, without binding them.

r[expr.assign.destructure.default-binding]
Note that default binding modes do not apply for the desugared expression.

r[expr.compound-assign]
## Compound assignment expressions

r[expr.compound-assign.syntax]
```grammar,expressions
CompoundAssignmentExpression ->
      Expression `+=` Expression
    | Expression `-=` Expression
    | Expression `*=` Expression
    | Expression `/=` Expression
    | Expression `%=` Expression
    | Expression `&=` Expression
    | Expression `|=` Expression
    | Expression `^=` Expression
    | Expression `<<=` Expression
    | Expression `>>=` Expression
```

r[expr.compound-assign.intro]
*Compound assignment expressions* combine arithmetic and logical binary operators with assignment expressions.

For example:

```rust
let mut x = 5;
x += 1;
assert!(x == 6);
```

The syntax of compound assignment is a [mutable] [place expression], the *assigned operand*, then one of the operators followed by an `=` as a single token (no whitespace), and then a [value expression], the *modifying operand*.

r[expr.compound-assign.place]
Unlike other place operands, the assigned place operand must be a place expression.

r[expr.compound-assign.no-value]
Attempting to use a value expression is a compiler error rather than promoting it to a temporary.

r[expr.compound-assign.operand-order]
Evaluation of compound assignment expressions depends on the types of the operators.

r[expr.compound-assign.primitive-order]
If both types are primitives, then the modifying operand will be evaluated first followed by the assigned operand.
It will then set the value of the assigned operand's place to the value of performing the operation of the operator with the values of the assigned operand and modifying operand.

> [!NOTE]
> This is different than other expressions in that the right operand is evaluated before the left one.

r[expr.compound-assign.trait]
Otherwise, this expression is syntactic sugar for calling the function of the overloading compound assignment trait of the operator (see the table earlier in this chapter).
A mutable borrow of the assigned operand is automatically taken.

For example, the following expression statements in `example` are equivalent:

```rust
# struct Addable;
# use std::ops::AddAssign;

impl AddAssign<Addable> for Addable {
    /* */
# fn add_assign(&mut self, other: Addable) {}
}

fn example() {
# let (mut a1, a2) = (Addable, Addable);
  a1 += a2;

# let (mut a1, a2) = (Addable, Addable);
  AddAssign::add_assign(&mut a1, a2);
}
```

r[expr.compound-assign.result]
Like assignment expressions, compound assignment expressions always produce [the unit value][unit].

> [!WARNING]
> The evaluation order of operands swaps depending on the types of the operands:
> with primitive types the right-hand side will get evaluated first, while with non-primitive types the left-hand side will get evaluated first.
> Try not to write code that depends on the evaluation order of operands in compound assignment expressions.
> See [this test] for an example of using this dependency.

[copies or moves]: ../expressions.md#moved-and-copied-types
[dropping]: ../destructors.md
[explicit discriminants]: ../items/enumerations.md#explicit-discriminants
[field-less enums]: ../items/enumerations.md#field-less-enum
[grouped expression]: grouped-expr.md
[literal expression]: literal-expr.md#integer-literal-expressions
[logical and]: ../types/boolean.md#logical-and
[logical not]: ../types/boolean.md#logical-not
[logical or]: ../types/boolean.md#logical-or
[logical xor]: ../types/boolean.md#logical-xor
[mutable]: ../expressions.md#mutability
[place expression]: ../expressions.md#place-expressions-and-value-expressions
[assignee expression]: ../expressions.md#place-expressions-and-value-expressions
[undefined behavior]: ../behavior-considered-undefined.md
[unit]: ../types/tuple.md
[Unit-only enums]: ../items/enumerations.md#unit-only-enum
[value expression]: ../expressions.md#place-expressions-and-value-expressions
[temporary value]: ../expressions.md#temporaries
[this test]: https://github.com/rust-lang/rust/blob/1.58.0/src/test/ui/expr/compound-assignment/eval-order.rs
[float-float]: https://github.com/rust-lang/rust/issues/15536
[Function pointer]: ../types/function-pointer.md
[Function item]: ../types/function-item.md
[undefined behavior]: ../behavior-considered-undefined.md
[Underscore expressions]: ./underscore-expr.md
[range expressions]: ./range-expr.md

<script>
(function() {
    var fragments = {
        "#slice-dst-pointer-to-pointer-cast": "operator-expr.html#pointer-to-pointer-cast",
    };
    var target = fragments[window.location.hash];
    if (target) {
        var url = window.location.toString();
        var base = url.substring(0, url.lastIndexOf('/'));
        window.location.replace(base + "/" + target);
    }
})();
</script>

r[expr.range]
# Range expressions

r[expr.range.syntax]
```grammar,expressions
RangeExpression ->
      RangeExpr
    | RangeFromExpr
    | RangeToExpr
    | RangeFullExpr
    | RangeInclusiveExpr
    | RangeToInclusiveExpr

RangeExpr -> Expression `..` Expression

RangeFromExpr -> Expression `..`

RangeToExpr -> `..` Expression

RangeFullExpr -> `..`

RangeInclusiveExpr -> Expression `..=` Expression

RangeToInclusiveExpr -> `..=` Expression
```

r[expr.range.behavior]
The `..` and `..=` operators will construct an object of one of the `Range` variants, according to the following table:

| Production             | Syntax        | Type                         | Range                 |
|------------------------|---------------|------------------------------|-----------------------|
| [RangeExpr]            | start`..`end  | [Range]            | start &le; x &lt; end |
| [RangeFromExpr]        | start`..`     | [RangeFrom]        | start &le; x          |
| [RangeToExpr]          | `..`end       | [RangeTo]          |            x &lt; end |
| [RangeFullExpr]        | `..`          | [RangeFull]        |            -          |
| [RangeInclusiveExpr]   | start`..=`end | [RangeInclusive]   | start &le; x &le; end |
| [RangeToInclusiveExpr] | `..=`end      | [RangeToInclusive] |            x &le; end |

where the builtin type `Range` is defined as:

```rust
struct Range {
    /// The lower bound of the range (inclusive).
    start: usize,
    /// The upper bound of the range (exclusive).
    end: usize,
}
```
<!-- 
Examples:

```rust
1..2;   // Range
3..;    // RangeFrom
..4;    // RangeTo
..;     // RangeFull
5..=6;  // RangeInclusive
..=7;   // RangeToInclusive
``` -->

r[expr.range.equivalence]
The following expressions are equivalent.

```rust
let x = Range {start: 0, end: 10};
let y = 0..10;

assert_eq!(x, y);
```

r[expr.range.slice]
Range expressions are used to index slices, arrays and strings.

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
let b: &[i32] = &a[1..4];
let s = "19260817";
let f: &str = &s[b[2] as usize..];
println(f); // 0817
```

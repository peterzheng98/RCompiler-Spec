r[type.unit]
# Unit type

r[type.tuple.syntax]
```grammar,types
UnitType -> `(` `)`
```

r[type.tuple.unit]
Unit type is actually the tuple type with no fields (`()`).
Its one value is also called *unit* or *the unit value*.

r[type.unit.usage]
The unit type is commonly used in:
- Functions without an explicit return type return `()` by default.
- A block `{ ... }` returns the value of its last expression; if absent, it returns `()`.
- Some expressions always evaluate to `()`. They are:
  - [Block expressions](../expressions/block-expr.md) that does not end with an expression.
  - Assignment expressions and Compound assignment expressions in [Operator expressions](../expressions/operator-expr.md#assignment-expressions).
  - [Loop expressions](../expressions/loop-expr.md) that do not break with a value.
  - [Return expressions](../expressions/return-expr.md) that do not return a value.

r[type.unit.example]
Some examples:
```rust
fn main() -> () {
  let mut x = 5;    
  let y: () = x = 8;
  let z: ();
  z = while (x < 10) {
      x += 1;
      printlnInt(x); // 9\n10\n
  };
  let mut w = 2;
  let a: [(); 5] = [w += 1; 5];
  printlnInt(w); // 3
  let b: [(); 3] = [w += 1, w += 1, w += 1];
  printlnInt(w); // 6
  let r: () = return;
}
```

r[type.unit.note]
The constructor `()` for unit type is not required. For example:
```rust
let x: () = (); // not required
```

r[items.const]
# Constant items

r[items.const.syntax]
```grammar,items
ConstantItem ->
    `const` IDENTIFIER `:` Type ( `=` Expression )? `;`
```

r[items.const.intro]
A *constant item* is an optionally named _[constant value]_ which is not associated
with a specific memory location in the program.

r[items.const.behavior]
Constants are essentially inlined wherever they are used, meaning that they are copied directly into the relevant
context when used. This includes usage of constants from external crates, and
non-[`Copy`] types. References to the same constant are not necessarily
guaranteed to refer to the same memory address.

r[items.const.namespace]
The constant declaration defines the constant value in the [value namespace] of the module or block where it is located.

r[items.const.static]
Constants must be explicitly typed.

```rust
const BIT1: u32 = 1 << 0;
const BIT2: u32 = 1 << 1;

const BITS: [u32; 2] = [BIT1, BIT2];
const STRING: &str = "bitstring";
```

r[items.const.no-mut-refs]
The final value of a `const` item cannot contain any mutable references `&mut`.

r[items.const.expr-omission]
The expression that defines the constant may only be omitted in a [trait definition].

r[items.const.eval]
## Evaluation

[Free][free] constants are always [evaluated][const_eval] at compile-time to surface
panics. Details in [const_eval].

[const_eval]: ../const_eval.md
[associated constant]: ../items/associated-items.md#associated-constants
[constant value]: ../const_eval.md#constant-expressions
[free]: ../glossary.md#free-item
[static lifetime elision]: ../lifetime-elision.md#const-and-static-elision
[trait definition]: traits.md
[underscore imports]: use-declarations.md#underscore-imports
[`Copy`]: ../special-types-and-traits.md#copy
[value namespace]: ../names/namespaces.md
[promotion]: ../destructors.md#constant-promotion

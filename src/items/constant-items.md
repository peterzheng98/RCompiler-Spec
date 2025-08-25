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
Constants must be explicitly typed. The type must have a `'static` lifetime: any
references in the initializer must have `'static` lifetimes. 

```rust
const BIT1: u32 = 1 << 0;
const BIT2: u32 = 1 << 1;

const BITS: [u32; 2] = [BIT1, BIT2];
const STRING: &'static str = "bitstring";

struct BitsNStrings<'a> {
    mybits: [u32; 2],
    mystring: &'a str,
}

const BITS_N_STRINGS: BitsNStrings<'static> = BitsNStrings {
    mybits: BITS,
    mystring: STRING,
};
```

r[items.const.no-mut-refs]
The final value of a `const` item cannot contain any mutable references.

```rust
# use core::sync::atomic::AtomicU8;
static S: AtomicU8 = AtomicU8::new(0);
const C: &AtomicU8 = &S; // OK
```

r[items.const.expr-omission]
The constant expression may only be omitted in a [trait definition].

r[items.const.unnamed]
## Unnamed constant

r[items.const.unnamed.intro]
Unlike an [associated constant], a [free] constant may be unnamed by using
an underscore instead of the name. For example:

```rust
const _: () =  { struct _SameNameTwice; };

// OK although it is the same name as above:
const _: () =  { struct _SameNameTwice; };
```

r[items.const.unnamed.repetition]
As with [underscore imports], macros may safely emit the same unnamed constant in
the same scope more than once. For example, the following should not produce an error:

```rust
macro_rules! m {
    ($item: item) => { $item $item }
}

m!(const _: () = (););
// This expands to:
// const _: () = ();
// const _: () = ();
```

r[items.const.eval]
## Evaluation

[Free][free] constants are always [evaluated][const_eval] at compile-time to surface
panics. This happens even within an unused function:

```rust,compile_fail
// Compile-time panic
const PANIC: () = std::unimplemented!();

fn unused_generic_function<T>() {
    // A failing compile-time assertion
    const _: () = assert!(usize::BITS == 0);
}
```

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

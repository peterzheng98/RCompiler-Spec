r[names.preludes]
# Preludes

r[names.preludes.intro]
A *prelude* is a collection of names that are automatically brought into scope
of every module in a crate.

These prelude names are not part of the module itself: they are implicitly
queried during [name resolution]. For example, even though something like
[`Box`] is in scope in every module, you cannot refer to it as `self::Box`
because it is not a member of the current module.

r[names.preludes.kinds]
There are several different preludes:

- [Preludes](#preludes)
  - [Standard library prelude](#standard-library-prelude)
    - [Types](#types)
      - [`String`](#string)
    - [Traits](#traits)
      - [`Copy` trait](#copy-trait)
  - [Builtin functions](#builtin-functions)
    - [print](#print)
    - [println](#println)
    - [printInt](#printint)
    - [printlnInt](#printlnint)
    - [getString](#getstring)
    - [getInt](#getint)
    - [exit](#exit)
  - [Builtin methods](#builtin-methods)
    - [to\_string](#to_string)
    - [as\_str](#as_str)
    - [len](#len)
  - [Language prelude](#language-prelude)

r[names.preludes.std]
## Standard library prelude

r[names.prelude.types-and-traits.types]
### Types

r[names.prelude.types-and-traits.types.String]
#### `String`

r[names.prelude.types-and-traits.traits]
### Traits

r[names.prelude.types-and-traits.traits.copy]
#### `Copy` trait
  - Types of integer family including `u32`, `i32` and `boolean` and the type `char` implement `Copy` trait inexplicitly, other basic types do not. Here "inexplicitly" means `Copy` trait only represents the feature of those types which implement it, but testcases will not involve it and may still involve the identifier `Copy` as that of customized types or traits.
  - Enums and tuples implement the trait if all of their fields do.
  - Since it is not defined to explicitly indicate `Copy` trait by `#[derive(Copy)]`, all customized structs do not implement `Copy` trait.

r[names.prelude.functions]
## Builtin functions

r[names.prelude.functions.intro]
Builtin functions operate on standard input (`stdin`) and standard output (`stdout`) unless otherwise stated.

r[names.prelude.functions.print]
### print

```rust
fn print(s: &str) -> ()
```

Writes the exact bytes of `s` to `stdout` without appending a newline.

```rust
print("Hello, world!");
```

r[names.prelude.functions.println]
### println

```rust
fn println(s: &str) -> ()
```

Writes the exact bytes of `s` to `stdout` and then writes a single newline character.

```rust
println("Hello, world!");
```

r[names.prelude.functions.printInt]
### printInt

```rust
fn printInt(n: i32) -> ()
```

Writes the decimal ASCII representation of `n` to `stdout` without a trailing newline.

```rust
printInt(42);
```

r[names.prelude.functions.printlnInt]
### printlnInt

```rust
fn printlnInt(n: i32) -> ()
```

Writes the decimal ASCII representation of `n` to `stdout` and then writes a single newline character.

```rust
printlnInt(42);
```

r[names.prelude.functions.getString]
### getString

```rust
fn getString() -> String
```

Reads one line from `stdin` and returns it as a `String`. The trailing newline, if any, is not included in the returned value.

```rust
let name: String = getString();
println(name.as_str());
```

r[names.prelude.functions.getInt]
### getInt

```rust
fn getInt() -> i32
```
Reads an integer token from `stdin` and returns it as an `i32`.

```rust
let a: i32 = getInt();
let b: i32 = getInt();
printlnInt(a + b);
```

r[names.prelude.functions.exit]
### exit

```rust
fn exit(code: i32) -> ()
```

Immediately terminates the current process and returns the provided exit `code` to the parent process. During type checking, `exit` is treated as returning `()` (this specification does not include a never type). Nevertheless, control flow does not continue after the call at runtime.

**`exit` may appear only as the final statement of the `main` function. Using `exit` anywhere else is a semantic error.**

```rust
fn main() -> () {
    println("done");
    exit(0);
}
```

The following is invalid:

```rust
fn main() -> () {
    exit(1);
    println("unreachable");  // compile-time error
}
```

r[names.prelude.methods]
## Builtin methods

r[names.prelude.methods.intro]
Builtin methods are provided by the compiler on specific receiver types. They are always available and require no trait imports or declarations.

r[names.prelude.methods.to_string]
### to_string

```rust
fn to_string(&self) -> String
```

Available on: `u32`, `usize`

Returns the decimal string representation of the receiver value. Does not allocate beyond the returned `String`; does not modify the receiver.

```rust
let x: u32 = 10;
let sx: String = x.to_string();
let y: usize = 42;
let sy: String = y.to_string();
println(sx.as_str());
println(sy.as_str());
```

r[names.prelude.methods.as_str]
### as_str

```rust
fn as_str(&self) -> &str
```

Available on: `String`

Returns a string slice view (`&str`) of the same underlying buffer; no allocation. The returned slice is valid as long as the original `String` is valid and not mutated in a way that would reallocate.

```rust
let s: String = getString();
let p: &str = s.as_str();
println(p);
```

r[names.prelude.methods.len]
### len

```rust
fn len(&self) -> u32
```

Available on: `[T; N]`, `&[T]`, `String`, `&str`

For `String` and `&str`, returns the number of bytes of the string (not character count). For arrays `[T; N]` and slices `&[T]`, returns the number of elements. 

For compile-time known sizes (e.g., arrays), the call is required to have no runtime overhead. 

For slices and strings, the operation is constant time.

```rust
let a: [i32; 3] = [1, 2, 3];
let n: u32 = a.len();           // 3

let s: String = getString();
let bytes: u32 = s.len();       // byte length of the string

let p: &str = "hello";
let k: u32 = p.len();           // 5

let sl: &[i32] = &a[..];
let m: u32 = sl.len();          // 3

// Printing a u32 requires an explicit cast to i32 for the builtin printInt/printlnInt
printlnInt(n as i32);
```


r[names.preludes.lang]
## Language prelude

r[names.preludes.lang.intro]
The language prelude includes names of types that are built-in
to the language. The language prelude is always in scope.

r[names.preludes.lang.entities]
It includes the following:

* [Type namespace]
    * [Boolean type] --- `bool`
    * [Textual types] --- `char` and `str`
    * [Integer types] --- `i32` and `u32`
    * [Machine-dependent integer types] --- `usize` and `isize`

[`extern crate`]: ../items/extern-crates.md
[`macro_use` attribute]: ../macros-by-example.md#the-macro_use-attribute
[`macro_use` prelude]: #macro_use-prelude
[`no_std` attribute]: #the-no_std-attribute
[`no_std` attribute]: #the-no_std-attribute
[attribute]: ../attributes.md
[Boolean type]: ../types/boolean.md
[Built-in attributes]: ../attributes.md#built-in-attributes-index
[extern prelude]: #extern-prelude
[floating-point types]: ../types/numeric.md#floating-point-types
[Integer types]: ../types/numeric.md#integer-types
[Language prelude]: #language-prelude
[Machine-dependent integer types]: ../types/numeric.md#machine-dependent-integer-types
[Macro namespace]: namespaces.md
[name resolution]: name-resolution.md
[Standard library prelude]: #standard-library-prelude
[Textual types]: ../types/textual.md
[tool attributes]: ../attributes.md#tool-attributes
[Tool prelude]: #tool-prelude
[Type namespace]: namespaces.md
[use declarations]: ../items/use-declarations.md

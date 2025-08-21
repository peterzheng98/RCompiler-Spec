r[ub.builtin]
# Builtin

r[ub.builtin.intro]
This chapter describes the builtin functions and methods provided by the compiler. These are always in scope and do not require imports or explicit declarations.

r[ub.builtin.types-and-traits]
## Special types and traits

r[ub.builtin.types-and-traits.identifiers]
It needs to be clarify that the identifiers of those undefined special types and traits are still legal as that of customized types or traits when testcases involves them.

r[ub.builtin.types-and-traits.types]
### Special types

Only the following three special types are defined, and other special types like `Rc<T>`, `Arc<T>` and `Pin<T>` are all not defined.

r[ub.builtin.types-and-traits.types.box]
#### Box<T>

`Box<T>` is not defined, so this language does not support heap allocation.

r[ub.builtin.types-and-traits.types.option]
#### Option<T>

`Option<T>` is not defined, so several corresponding features are also not defined, which are listed below:
- `if let` and `while let` patterns;
- `match` expressions;

r[ub.builtin.types-and-traits.types.result]
#### Result<T, E>

`Result<T, E>` is not defined.

r[ub.builtin.types-and-traits.traits]
#### Special traits and auto traits

All special traits mentioned in [Special types and traits](../special-types-and-traits.md) are not defined. And all features with regard to auto traits are also not defined.

Instead, we defines that

r[ub.builtin.types-and-traits.traits.copy]
- `Copy` trait
  - Types of integer family including `u32`, `i32` and `boolean` and the type `char` implement `Copy` trait inexplicitly, other basic types do not. Here "inexplicitly" means `Copy` trait only represents the feature of those types which implement it, but testcases will not involve it and may still involve the identifier `Copy` as that of customized types or traits.
  - Enums and tuples implement the trait if all of their fields do.
  - Since it is not defined to explicitly indicate `Copy` trait by `#[derive(Copy)]`, all customized structs do not implement `Copy` trait.
  - For types that do not implement `Copy` trait, a simple assignment like `let n2 = n1;` neither copys nor moves the value. Instead, `n2` becomes an alias to the same memory as `n1` (same as reference in C++).

r[ub.builtin.functions]
## Builtin functions

r[ub.builtin.functions.intro]
Builtin functions operate on standard input (`stdin`) and standard output (`stdout`) unless otherwise stated.

r[ub.builtin.functions.print]
### `print`

```rust
fn print(s: &str) -> ()
```

Writes the exact bytes of `s` to `stdout` without appending a newline.

```rust
print("Hello, world!");
```

r[ub.builtin.functions.println]
### `println`

```rust
fn println(s: &str) -> ()
```

Writes the exact bytes of `s` to `stdout` and then writes a single newline character.

```rust
println("Hello, world!");
```

r[ub.builtin.functions.printInt]
### `printInt`

```rust
fn printInt(n: i32) -> ()
```

Writes the decimal ASCII representation of `n` to `stdout` without a trailing newline.

```rust
printInt(42);
```

r[ub.builtin.functions.printlnInt]
### `printlnInt`

```rust
fn printlnInt(n: i32) -> ()
```

Writes the decimal ASCII representation of `n` to `stdout` and then writes a single newline character.

```rust
printlnInt(42);
```

r[ub.builtin.functions.getString]
### `getString`

```rust
fn getString() -> String
```

Reads one line from `stdin` and returns it as a `String`. The trailing newline, if any, is not included in the returned value.

```rust
let name: String = getString();
println(name.as_str());
```

r[ub.builtin.functions.getInt]
### `getInt`

```rust
fn getInt() -> i32
```
Reads an integer token from `stdin` and returns it as an `i32`.

```rust
let a: i32 = getInt();
let b: i32 = getInt();
printlnInt(a + b);
```

r[ub.builtin.functions.exit]
### `exit`

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

r[ub.builtin.methods]
## Builtin methods

r[ub.builtin.methods.intro]
Builtin methods are provided by the compiler on specific receiver types. They are always available and require no trait imports or declarations.

r[ub.builtin.methods.to_string]
### `to_string`

```rust
fn to_string(&self) -> String
```

Available on: `u32`, `usize`

Returns the decimal string representation of the receiver value. Does not allocate beyond the returned `String`; does not modify the receiver. This method can be called on both immutable and mutable references.

```rust
let x: u32 = 10;
let sx: String = x.to_string();
let y: usize = 42;
let sy: String = y.to_string();

let mut z: u32 = 15;
let sz: String = z.to_string(); // works on mutable values too

println(sx.as_str());
println(sy.as_str());
println(sz.as_str());
```

r[ub.builtin.methods.as_str]
### `as_str` and `as_mut_str`

```rust
impl String {
  fn as_str(&self) -> &str
  fn as_mut_str(&mut self) -> &mut str
}
```

Available on: `String`

Returns a string slice view (`&str` for `as_str` while `&mut str` for `as_mut_str`) of the same underlying buffer; no allocation. The returned slice is valid as long as the original `String` is valid and not mutated in a way that would reallocate. Note that `as_mut_str` only accepts mutable references.

```rust
let s: String = getString();
let p: &str = s.as_str();
let mut s_mut: String = getString();
let p_mut: &mut str = s_mut.as_mut_str();
println(p);
println(p_mut);
```

r[ub.builtin.methods.len]
### `len`

```rust
fn len(&self) -> usize
```

Available on: `[T; N]`, `&[T; N]`, `&mut [T; N]`, `String`, `&str`, `&mut str`

For `String`, `&str`, and `&mut str`, returns the number of bytes of the string (not character count). For arrays `[T; N]` and array references `&[T; N]`, `&mut [T; N]`, returns the number of elements. 

For compile-time known sizes (e.g., arrays), the call is required to have no runtime overhead. 

For array references and strings, the operation is constant time.

```rust
let a: [i32; 3] = [1, 2, 3];
let n: usize = a.len();           // 3

let s: String = getString();
let bytes: usize = s.len();       // byte length of the string

let p: &str = "hello";
let k: usize = p.len();           // 5

let a_ref: &[i32; 3] = &a;
let m: usize = a_ref.len();       // 3

let mut a_mut: [i32; 3] = [1, 2, 3];
let a_mut_ref: &mut [i32; 3] = &mut a_mut;
let m_mut: usize = a_mut_ref.len(); // 3

let mut s_mut: String = getString();
let p_mut: &mut str = s_mut.as_mut_str();
let k_mut: usize = p_mut.len();  // byte length of the mutable string slice

// Printing a usize requires an explicit cast to i32 for the builtin printInt/printlnInt
printlnInt(n as i32);
```

r[ub.builtin.string]
## `String` in std

r[ub.builtin.string.from]
### `from`

```rust
fn from(&str) -> String
fn from(&mut str) -> String
```

Converts a `&str` or `&mut str` into a `String`. The result is allocated on the heap.

```rust
let s: &str = "s";
let mut string_mut: String = String::from(s);
let s_mut: &mut str = string_mut.as_mut_str();
let string: String = String::from(s_mut);
```

r[ub.builtin.string.append]
### `append`

```rust
fn append(&mut self, s: &str) -> ()
```

Appends the given string slice to the end of this `String`. This method modifies the `String` in place.

```rust
let mut string: String = String::from("Hello");
string.append(", world!");
println(string.as_str()); // prints "Hello, world!"
```

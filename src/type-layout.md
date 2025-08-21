r[layout]
# Type Layout

r[layout.intro]
The layout of a type is its size, alignment, and the relative offsets of its
fields. For enums, how the discriminant is laid out and interpreted is also part
of type layout.

r[layout.properties]
## Size and Alignment

All values have an alignment and size.

r[layout.properties.align]
The *alignment* of a value specifies what addresses are valid to store the value
at. A value of alignment `n` must only be stored at an address that is a
multiple of n. For example, a value with an alignment of 2 must be stored at an
even address, while a value with an alignment of 1 can be stored at any address.
Alignment is measured in bytes, and must be at least 1, and always a power of 2.
The alignment of a value can be checked with the [`align_of_val`] function.

r[layout.properties.size]
The *size* of a value is the offset in bytes between successive elements in an
array with that item type including alignment padding. The size of a value is
always a multiple of its alignment. Note that some types are zero-sized; 0 is
considered a multiple of any alignment (for example, on some platforms, the type
`[u16; 0]` has size 0 and alignment 2). The size of a value can be checked with
the [`size_of_val`] function.

r[layout.properties.sized]
Types where all values have the same size and alignment, and both are known at
compile time, implement the [`Sized`] trait and can be checked with the
[`size_of`] and [`align_of`] functions. Types that are not [`Sized`] are known
as [dynamically sized types]. Since all values of a `Sized` type share the same
size and alignment, we refer to those shared values as the size of the type and
the alignment of the type respectively.

r[layout.primitive]
## Primitive Data Layout

r[layout.primitive.size]
The size of most primitives is given in this table.

| Type              | `size_of::<Type>()`|
|--                 |--                  |
| `bool`            | 1                  |
| `u8` / `i8`       | 1                  |
| `u16` / `i16`     | 2                  |
| `u32` / `i32`     | 4                  |
| `u64` / `i64`     | 8                  |
| `u128` / `i128`   | 16                 |
| `usize` / `isize` | See below          |
| `f32`             | 4                  |
| `f64`             | 8                  |
| `char`            | 4                  |

r[layout.primitive.size-int]
`usize` and `isize` have a size big enough to contain every address on the
target platform. For example, on a 32 bit target, this is 4 bytes, and on a 64
bit target, this is 8 bytes.

r[layout.primitive.align]
The alignment of primitives is platform-specific.
In most cases, their alignment is equal to their size, but it may be less.
In particular, `i128` and `u128` are often aligned to 4 or 8 bytes even though
their size is 16, and on many 32-bit platforms, `i64`, `u64`, and `f64` are only
aligned to 4 bytes, not 8.

r[layout.pointer]
## Pointers and References Layout

r[layout.pointer.intro]
Pointers and references have the same layout. Mutability of the pointer or
reference does not change the layout.

r[layout.pointer.thin]
Pointers to sized types have the same size and alignment as `usize`.

r[layout.pointer.unsized]
Pointers to unsized types are sized. The size and alignment is guaranteed to be
at least equal to the size and alignment of a pointer.

> [!NOTE]
> Though you should not rely on this, all pointers to <abbr title="Dynamically Sized Types">DSTs</abbr> are currently twice the size of the size of `usize` and have the same alignment.

r[layout.array]
## Array Layout

An array of `[T; N]` has a size of `size_of::<T>() * N` and the same alignment
of `T`. Arrays are laid out so that the zero-based `nth` element of the array
is offset from the start of the array by `n * size_of::<T>()` bytes.

r[layout.str]
## `str` Layout

String slices are a UTF-8 representation of characters that have the same layout as slices of type `[u8]`. A reference `&str` has the same layout as a reference `&[u8]`.

[`align_of_val`]: std::mem::align_of_val
[`size_of_val`]: std::mem::size_of_val
[`align_of`]: std::mem::align_of
[`size_of`]: std::mem::size_of
[`Sized`]: std::marker::Sized
[`Copy`]: std::marker::Copy
[dynamically sized types]: dynamically-sized-types.md
[field-less enums]: items/enumerations.md#field-less-enum
[fn-abi-compatibility]: ../core/primitive.fn.md#abi-compatibility
[enumerations]: items/enumerations.md
[zero-variant enums]: items/enumerations.md#zero-variant-enums
[undefined behavior]: behavior-considered-undefined.md
[`PhantomData<T>`]: special-types-and-traits.md#phantomdatat
[`Rust`]: #the-rust-representation
[`C`]: #the-c-representation
[primitive representations]: #primitive-representations
[structs]: items/structs.md
[`transparent`]: #the-transparent-representation
[`Layout`]: std::alloc::Layout

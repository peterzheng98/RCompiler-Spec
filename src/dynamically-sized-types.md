r[dynamic-sized]
# Dynamically Sized Types

r[dynamic-sized.intro]
Most types have a fixed size that is known at compile time. A type with a size that is known only at run-time is called a _dynamically sized type_ (_DST_) or, informally, an unsized type.  Slices, trait objects, and [str] are examples of <abbr title="dynamically sized types">DSTs</abbr>. In RCompiler, we have only `str`.

r[dynamic-sized.restriction]
Such types can only be used in certain cases:

r[dynamic-sized.pointer-types]
* [Pointer types] to <abbr title="dynamically sized types">DSTs</abbr> are sized but have twice the size of pointers to sized types
    * Pointers to `str` also store the number of elements.

<!-- r[dynamic-sized.struct-field]
* Structs may contain a <abbr title="dynamically sized type">DST</abbr> as the
  last field; this makes the struct itself a
  <abbr title="dynamically sized type">DST</abbr>. -->

> [!NOTE]
> Variables, function parameters and [const] items must be `Sized`.

[Slices]: types/slice.md
[str]: types/textual.md
[Pointer types]: types/pointer.md
[const]: items/constant-items.md

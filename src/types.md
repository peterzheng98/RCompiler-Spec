{{#include types-redirect.html}}
r[type]
# Types

r[type.intro]
Every variable, item, and value in a Rust program has a type. The _type_ of a
*value* defines the interpretation of the memory holding it and the operations
that may be performed on the value.

r[type.builtin]
Built-in types are tightly integrated into the language, in nontrivial ways
that are not possible to emulate in user-defined types.

r[type.user-defined]
User-defined types have limited capabilities.

r[type.kinds]
The list of types is:

* Primitive types:
    * [Boolean] --- `bool`
    * [Numeric] --- integer
    * [Textual] --- `char` and `str`
* Sequence types:
    * [Array]
    * [Slice]
* User-defined types:
    * [Struct]
    * [Enum]
* Pointer types:
    * [References]
* Unit type:
    * [Unit] --- `()`

r[type.name]
## Type expressions

r[type.name.syntax]
```grammar,types
Type -> TypeNoBounds

TypeNoBounds ->
      TypePath
    | ReferenceType
    | ArrayType
    | SliceType
    | InferredType
    | UnitType
```

r[type.name.intro]
A _type expression_ as defined in the [Type] grammar rule above is the syntax
for referring to a type. It may refer to:

r[type.name.sequence]
* Sequence types ([array], [slice]).

r[type.name.path]
* [Type paths] which can reference:
    * Primitive types ([boolean], [numeric], [textual]).
    * Paths to an [item] ([struct], [enum], [union], [type alias], [trait]).
    * [`Self` path] where `Self` is the implementing type.

r[type.name.pointer]
* Pointer types ([reference]).

r[type.name.inference]
* The [inferred type] which asks the compiler to determine the type.

r[type.recursive]
## Recursive types

r[type.recursive.intro]
Nominal types &mdash; [structs] &mdash; may be
recursive. That is, each `struct` field may
refer, directly or indirectly, to the enclosing `struct` type itself.

r[type.recursive.constraint]
Such recursion has restrictions:

* Recursive types must include a nominal type in the recursion (not other structural types such as [arrays]). So `type
  Rec = &'static [Rec]` is not allowed.
* The size of a recursive type must be finite; in other words the recursive
  fields of the type must be [pointer types].

An example of a *recursive* type and its use:

```rust
struct List {
    data: usize,
    // A struct can reference itself (as long as it is not 
    // infinitely recursive).
    next: Option<Box<Self>>,
}

impl List {
    fn new(data: usize) -> Self {
        List {
            data,
            next: None,
        }
    }

    fn append(&mut self, value: usize) {
        match &mut self.next {
            Some(next) => next.append(value),
            None => self.next = Some(Box::new(Self::new(value))),
        }
    }
}
```

[Array]: types/array.md
[Boolean]: types/boolean.md
[Closures]: types/closure.md
[Enum]: types/enum.md
[Function pointers]: types/function-pointer.md
[Functions]: types/function-item.md
[Impl trait]: types/impl-trait.md
[Macros]: macros.md
[Numeric]: types/numeric.md
[Parentheses]: #parenthesized-types
[Raw pointers]: types/pointer.md#raw-pointers-const-and-mut
[References]: types/pointer.md#shared-references-
[Slice]: types/slice.md
[Struct]: types/struct.md
[Textual]: types/textual.md
[Trait objects]: types/trait-object.md
[Tuple]: types/tuple.md
[Type paths]: paths.md#paths-in-types
[Union]: types/union.md
[`Self` path]: paths.md#self-1
[arrays]: types/array.md
[enumerations]: types/enum.md
[function pointer]: types/function-pointer.md
[inferred type]: types/inferred.md
[item]: items.md
[never]: types/never.md
[pointer types]: types/pointer.md
[raw pointer]: types/pointer.md#raw-pointers-const-and-mut
[reference type]: types/pointer.md#shared-references-
[reference]: types/pointer.md#shared-references-
[structs]: types/struct.md
[trait]: types/trait-object.md
[tuples]: types/tuple.md
[type alias]: items/type-aliases.md
[type aliases]: items/type-aliases.md
[type boundaries]: trait-bounds.md
[type parameters]: types/parameters.md
[unions]: types/union.md
[Unit]: types/unit.md

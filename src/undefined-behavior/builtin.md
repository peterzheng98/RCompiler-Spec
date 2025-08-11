r[ub.builtin]
# Builtin

r[ub.builtin.types-and-traits]
## Special types and traits

r[ub.builtin.types-and-traits.identifiers]
It needs to be clarify that the identifiers of those undefined special types and traits are still legal as that of customized types or traits when testcases involves them.

r[ub.builtin.types-and-traits.types]
### Special types

Only the following three special types are defined, and other special types like `Rc<T>`, `Arc<T>` and `Pin<T>` are all not defined.

r[ub.builtin.types-and-traits.types.box]
#### Box<T>

It remains to be considered.

r[ub.builtin.types-and-traits.types.option]
#### Option<T>

It remains to be considered.

r[ub.builtin.types-and-traits.types.result]
#### Result<T, E>

It remains to be considered.

r[ub.builtin.types-and-traits.traits]
#### Special traits and auto traits

All special traits mentioned in [Special types and traits](../special-types-and-traits.md) are not defined. And all features with regard to auto traits are also not defined.

Instead, we defines that

r[ub.builtin.types-and-traits.traits.copy]
- `Copy` trait
  - Types of integer family including `u32`, `i32` and `boolean` and the type `char` implement `Copy` trait inexplicitly, other basic types do not. Here "inexplicitly" means `Copy` trait only represents the feature of those types which implement it, but testcases will not involve it and may still involve the identifier `Copy` as that of customized types or traits.
  - Enums and tuples implement the trait if all of their fields do.
  - Since it is not defined to explicitly indicate `Copy` trait by `#[derive(Copy)]`, all customized structs do not implement `Copy` trait.

r[ub.builtin.functions]
## Builtin functions

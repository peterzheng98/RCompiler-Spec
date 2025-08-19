r[items.impl]
# Implementations

r[items.impl.syntax]
```grammar,items
Implementation -> InherentImpl | TraitImpl

InherentImpl ->
    `impl` Type `{`
        AssociatedItem*
    `}`

TraitImpl ->
    `impl` IDENTIFIER `for` Type
    `{`
        AssociatedItem*
    `}`
```

r[items.impl.intro]
An _implementation_ is an item that associates items with an _implementing type_.
Implementations are defined with the keyword `impl` and contain functions
that belong to an instance of the type that is being implemented or to the
type statically.

r[items.impl.kinds]
There are two types of implementations:

- inherent implementations
- [trait] implementations

r[items.impl.trait.foreign-types]
The implementing type is either a [struct] or an [enumeration] defined in the [type namespace](../names/namespaces.md#r-names.namespaces.kinds) that is visible to the `impl` item.

r[items.impl.inherent]
## Inherent Implementations

r[items.impl.inherent.implementing-type]
The nominal type is called the _implementing type_ and the associable items are
the _associated items_ to the implementing type.

r[items.impl.inherent.associated-items]
Inherent implementations associate the contained items to the
implementing type.

r[items.impl.inherent.associated-items.allowed-items]
Inherent implementations can contain [associated functions] (including [methods]) and [associated constants].

r[items.impl.inherent.type-alias]
They cannot contain associated type aliases.

r[items.impl.inherent.coherence]
A type can also have multiple inherent implementations. An implementing type
must be defined within the same crate as the original type definition.

``` rust
pub mod color {
    pub struct Color(pub u8, pub u8, pub u8);

    impl Color {
        pub const WHITE: Color = Color(255, 255, 255);
    }
}

mod values {
    use super::color::Color;
    impl Color {
        pub fn red() -> Color {
            Color(255, 0, 0)
        }
    }
}

pub use self::color::Color;
fn main() {
    // Actual path to the implementing type and impl in the same module.
    color::Color::WHITE;

    // Impl blocks in different modules are still accessed through a path to the type.
    color::Color::red();

    // Re-exported paths to the implementing type also work.
    Color::red();

    // Does not work, because use in `values` is not pub.
    // values::Color::red();
}
```

r[items.impl.trait]
## Trait Implementations

r[items.impl.trait.implemented-trait]
The trait is known as the _implemented trait_. The implementing type
implements the implemented trait.

r[items.impl.trait.def-requirement]
A trait implementation must define all non-default associated items declared
by the implemented trait, may redefine default associated items defined by the
implemented trait, and cannot define any other items.

r[items.impl.trait.associated-item-path]
The path to the associated items is `<` followed by a path to the implementing
type followed by `as` followed by a path to the trait followed by `>` as a path
component followed by the associated item's path component.

```rust
# #[derive(Copy, Clone)]
# struct Point {x: f64, y: f64};
# type Surface = i32;
# struct BoundingBox {x: f64, y: f64, width: f64, height: f64};
# trait Shape { fn draw(&self, s: Surface); fn bounding_box(&self) -> BoundingBox; }
# fn do_draw_circle(s: Surface, c: Circle) { }
struct Circle {
    radius: f64,
    center: Point,
}

impl Copy for Circle {}

impl Clone for Circle {
    fn clone(&self) -> Circle { *self }
}

impl Shape for Circle {
    fn draw(&self, s: Surface) { do_draw_circle(s, *self); }
    fn bounding_box(&self) -> BoundingBox {
        let r = self.radius;
        BoundingBox {
            x: self.center.x - r,
            y: self.center.y - r,
            width: 2.0 * r,
            height: 2.0 * r,
        }
    }
}
```

r[items.impl.trait.coherence]
### Trait Implementation Coherence

r[items.impl.trait.coherence.intro]
A trait implementation is considered incoherent if either the type is foreign
or there are overlapping implementation instances (i.e. the same trait implemented for the same type),
and such incoherency should not be allowed by your compiler. 


[trait]: traits.md
[associated constants]: associated-items.md#associated-constants
[associated functions]: associated-items.md#associated-functions-and-methods
[associated type]: associated-items.md#associated-types
[attributes]: ../attributes.md
[bounds]: ../trait-bounds.md
[`cfg`]: ../conditional-compilation.md
[`deprecated`]: ../attributes/diagnostics.md#the-deprecated-attribute
[`doc`]: ../../rustdoc/the-doc-attribute.html
[generic parameters]: generics.md
[methods]: associated-items.md#methods
[path]: ../paths.md
[the lint check attributes]: ../attributes/diagnostics.md#lint-check-attributes
[Unsafe traits]: traits.md#unsafe-traits
[local trait]: ../glossary.md#local-trait
[local type]: ../glossary.md#local-type
[fundamental types]: ../glossary.md#fundamental-type-constructors
[uncovered type]: ../glossary.md#uncovered-type

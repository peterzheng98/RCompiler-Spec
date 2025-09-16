r[items.traits]
# Traits

r[items.traits.syntax]
```grammar,items
Trait ->
    `trait` IDENTIFIER
    `{`
        AssociatedItem*
    `}`
```

r[items.traits.intro]
A _trait_ describes an abstract interface that types can implement. This
interface consists of [associated items], which come in three varieties:

- [functions](associated-items.md#associated-functions-and-methods)
- [constants](associated-items.md#associated-constants)
<!-- - [types](associated-items.md#associated-types) -->

r[items.traits.namespace]
The trait declaration defines a trait in the [type namespace] of the module or block where it is located.

r[items.traits.associated-item-namespaces]
Associated items are defined as members of the trait within their respective namespaces. Associated types are defined in the type namespace. Associated constants and associated functions are defined in the value namespace.

r[items.traits.self-param]
All traits define an implicit type parameter `Self` that refers to "the type
that is implementing this interface". Traits do not contain additional type
parameters.

r[items.traits.impls]
Traits are implemented for specific types through separate [implementations].

r[items.traits.associated-item-decls]
Trait functions may omit the function body by replacing it with a semicolon.
This indicates that the implementation must define the function. If the trait
function defines a body, this definition acts as a default for any
implementation which does not override it. Similarly, associated constants may
omit the equals sign and expression to indicate implementations must define
the constant value. Associated types must never define the type, the type may
only be specified in an implementation.

```rust
// Examples of associated trait items with and without definitions.
trait Example {
    const CONST_NO_DEFAULT: i32;
    const CONST_WITH_DEFAULT: i32 = 99;
    type TypeNoDefault;
    fn method_without_default(&self);
    fn method_with_default(&self) {}
}
```

r[items.traits.const-fn]
Trait functions are not allowed to be [`const`].

[WildcardPattern]: ../patterns.md#wildcard-pattern
[bounds]: ../trait-bounds.md
[trait object]: ../types/trait-object.md
[associated items]: associated-items.md
[method]: associated-items.md#methods
[supertraits]: #supertraits
[implementations]: implementations.md
[generics]: generics.md
[where clauses]: generics.md#where-clauses
[generic functions]: functions.md#generic-functions
[unsafe]: ../unsafety.md
[trait implementation]: implementations.md#trait-implementations
[`Send`]: ../special-types-and-traits.md#send
[`Sync`]: ../special-types-and-traits.md#sync
[`Arc<Self>`]: ../special-types-and-traits.md#arct
[`Box<Self>`]: ../special-types-and-traits.md#boxt
[`Pin<P>`]: ../special-types-and-traits.md#pinp
[`Rc<Self>`]: ../special-types-and-traits.md#rct
[`async`]: functions.md#async-functions
[`const`]: functions.md#const-functions
[type namespace]: ../names/namespaces.md

<script>
(function() {
    var fragments = {
        "#object-safety": "traits.html#dyn-compatibility",
    };
    var target = fragments[window.location.hash];
    if (target) {
        var url = window.location.toString();
        var base = url.substring(0, url.lastIndexOf('/'));
        window.location.replace(base + "/" + target);
    }
})();
</script>

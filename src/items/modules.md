r[items.mod]
# Modules

r[items.mod.syntax]
```grammar,items
Module ->
     `mod` IDENTIFIER `;`
    |`mod` IDENTIFIER `{`
        InnerAttribute*
        Item*
      `}`
```

r[items.mod.intro]
A module is a container for zero or more [items].

r[items.mod.def]
A _module item_ is a module, surrounded in braces, named, and prefixed with the
keyword `mod`. A module item introduces a new, named module into the tree of
modules making up a crate.

r[items.mod.nesting]
Modules can nest arbitrarily.

An example of a module:

```rust
mod math {
    type Complex = (f64, f64);
    fn sin(f: f64) -> f64 {
        /* ... */
#       unimplemented!();
    }
    fn cos(f: f64) -> f64 {
        /* ... */
#       unimplemented!();
    }
    fn tan(f: f64) -> f64 {
        /* ... */
#       unimplemented!();
    }
}
```

r[items.mod.namespace]
Modules are defined in the [type namespace] of the module or block where they are located.

r[items.mod.multiple-items]
It is an error to define multiple items with the same name in the same namespace within a module.
See the [scopes chapter] for more details on restrictions and shadowing behavior.

The module in our project is all placed in exactly one file.


[`cfg`]: ../conditional-compilation.md
[`deprecated`]: ../attributes/diagnostics.md#the-deprecated-attribute
[`doc`]: ../../rustdoc/the-doc-attribute.html
[`no_implicit_prelude`]: ../names/preludes.md#the-no_implicit_prelude-attribute
[`path`]: #the-path-attribute
[attribute]: ../attributes.md
[items]: ../items.md
[module path]: ../paths.md
[scopes chapter]: ../names/scopes.md
[the lint check attributes]: ../attributes/diagnostics.md#lint-check-attributes
[type namespace]: ../names/namespaces.md

<script>
(function() {
    var fragments = {
        "#prelude-items": "../names/preludes.html",
    };
    var target = fragments[window.location.hash];
    if (target) {
        var url = window.location.toString();
        var base = url.substring(0, url.lastIndexOf('/'));
        window.location.replace(base + "/" + target);
    }
})();
</script>

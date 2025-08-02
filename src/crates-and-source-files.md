r[crate]
# Crates and source files

In our language, there is only one crate. However, you can explore how to extend your code to multiple crate.

r[crate.syntax]
```grammar,items
@root Crate ->
    InnerAttribute*
    Item*
```

> [!NOTE]
> Although Rust, like any other language, can be implemented by an interpreter as well as a compiler, the only existing implementation is a compiler, and the language has always been designed to be compiled. For these reasons, this section assumes a compiler.

r[crate.compile-time]
Rust's semantics obey a *phase distinction* between compile-time and
run-time.[^phase-distinction] Semantic rules that have a *static
interpretation* govern the success or failure of compilation, while
semantic rules that have a *dynamic interpretation* govern the behavior of the
program at run-time.

r[crate.unit]
The compilation model centers on artifacts called _crates_. Each compilation
processes a single crate in source form, and if successful, produces a single
crate in binary form: either an executable or some sort of
library.[^cratesourcefile]

r[crate.module]
A _crate_ is a unit of compilation and linking, as well as versioning,
distribution, and runtime loading. A crate contains a _tree_ of nested
[module] scopes. The top level of this tree is a module that is
anonymous (from the point of view of paths within the module) and any item
within a crate has a canonical [module path] denoting its location
within the crate's module tree.

r[crate.input-source]
The Rust compiler is always invoked with a single source file as input, and
always produces a single output crate. The processing of that source file may
result in other source files being loaded as modules. Source files have the
extension `.rs`.

r[crate.module-def]
A Rust source file describes a module, the name and location of which &mdash;
in the module tree of the current crate &mdash; are defined from outside the
source file: either by an explicit [Module][grammar-Module] item in a referencing
source file, or by the name of the crate itself.

r[crate.inline-module]
Every source file is a
module, but not every module needs its own source file: [module
definitions][module] can be nested within one file.

r[crate.items]
Each source file contains a sequence of zero or more [Item] definitions, and
may optionally begin with any number of [attributes]
that apply to the containing module, most of which influence the behavior of
the compiler.

r[crate.main]
## Main Functions

r[crate.main.general]
A crate that contains a `main` [function] can be compiled to an executable.

r[crate.main.restriction]
If a `main` function is present, it must take no arguments, must not declare any
[trait or lifetime bounds], must not have any [where clauses], and its return
type must implement the [`Termination`] trait.

```rust
fn main() {}
```
```rust
fn main() -> ! {
    _exit(0);
}
```

For return value of main function, you can assume that the function has the following signature:
```rust
// SAFETY: The function declarations given below are in
// line with the header files of `my_c_library`.
#[link(name = "my_c_library")]
unsafe extern "C" {
    fn _exit(x: i32); // no return function
}
```


[Unicode alphanumeric]: char::is_alphanumeric
[`!`]: types/never.md
[`ExitCode`]: std::process::ExitCode
[`Infallible`]: std::convert::Infallible
[`Termination`]: std::process::Termination
[attribute]: attributes.md
[attributes]: attributes.md
[function]: items/functions.md
[module]: items/modules.md
[module path]: paths.md
[panic-docs]: panic.md#unwinding-across-ffi-boundaries
[shebang]: input-format.md#shebang-removal
[trait or lifetime bounds]: trait-bounds.md
[where clauses]: items/generics.md#where-clauses

<script>
(function() {
    var fragments = {
        "#preludes-and-no_std": "names/preludes.html",
    };
    var target = fragments[window.location.hash];
    if (target) {
        var url = window.location.toString();
        var base = url.substring(0, url.lastIndexOf('/'));
        window.location.replace(base + "/" + target);
    }
})();
</script>

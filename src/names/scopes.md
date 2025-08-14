r[names.scopes]
# Scopes

r[names.scopes.intro]
A *scope* is the region of source text where a named [entity] may be referenced with that name.
The following sections provide details on the scoping rules and behavior, which depend on the kind of entity and where it is declared.
The process of how names are resolved to entities is described in the [name resolution] chapter.
More information on "drop scopes" used for the purpose of running destructors may be found in the [destructors] chapter.

r[names.scopes.items]
## Item scopes

r[names.scopes.items.module]
The name of an [item][items] declared directly in a [module] has a scope that extends from the start of the module to the end of the module. These items are also members of the module and can be referred to with a [path] leading from their module.

r[names.scopes.items.statement]
The name of an item declared as a [statement] has a scope that extends from the start of the block the item statement is in until the end of the block.

r[names.scopes.items.duplicate]
It is an error to introduce an item with a duplicate name of another item in the same [namespace] within the same module or block.
[Asterisk glob imports] have special behavior for dealing with duplicate names and shadowing, see the linked chapter for more details.

r[names.scopes.items.shadow-prelude]
Items in a module may shadow items in a [prelude](#prelude-scopes).

r[names.scopes.items.nested-modules]
Item names from outer modules are not in scope within a nested module.
A [path] may be used to refer to an item in another module.

r[names.scopes.associated-items]
### Associated item scopes

r[names.scopes.associated-items.scope]
[Associated items] are not scoped and can only be referred to by using a [path] leading from the type or trait they are associated with.
[Methods] can also be referred to via [call expressions].

r[names.scopes.associated-items.duplicate]
Similar to items within a module or block,  it is an error to introduce an item within a trait or implementation that is a duplicate of another item in the trait or impl in the same namespace.

r[names.scopes.pattern-bindings]
## Pattern binding scopes

The scope of a local variable [pattern] binding depends on where it is used:

r[names.scopes.pattern-bindings.let]
* [`let` statement] bindings range from just after the `let` statement until the end of the block where it is declared.
r[names.scopes.pattern-bindings.parameter]
* [Function parameter] bindings are within the body of the function.
r[names.scopes.pattern-bindings.closure]
* [Closure parameter] bindings are within the closure body.
r[names.scopes.pattern-bindings.loop]
* [`for`] bindings are within the loop body.
r[names.scopes.pattern-bindings.let-chains]
* [`if let`] and [`while let`] bindings are valid in the following conditions as well as the consequent block.
r[names.scopes.pattern-bindings.match-arm]
* [`match` arms] bindings are within the [match guard] and the match arm expression.

r[names.scopes.pattern-bindings.items]
Local variable scopes do not extend into item declarations.
<!-- Not entirely, see https://github.com/rust-lang/rust/issues/33118 -->

### Pattern binding shadowing

r[names.scopes.pattern-bindings.shadow]
Pattern bindings are allowed to shadow any name in scope with the following exceptions which are an error:

* [Const generic parameters]
* [Static items]
* [Const items]
* Constructors for [structs] and [enums]

The following example illustrates how local bindings can shadow item declarations:

```rust
fn shadow_example() {
    // Since there are no local variables in scope yet, this resolves to the function.
    foo(); // prints `function`
    let foo = || println!("closure");
    fn foo() { println!("function"); }
    // This resolves to the local closure since it shadows the item.
    foo(); // prints `closure`
}
```

r[names.scopes.prelude]
## Prelude scopes

r[names.scopes.prelude.intro]
[Preludes] bring entities into scope of every module.
The entities are not members of the module, but are implicitly queried during name resolution.

r[names.scopes.prelude.layers]
The preludes are layered such that one shadows another if they contain entities of the same name.
The order that preludes may shadow other preludes is the following where earlier entries may shadow later ones:

1. [Standard library prelude]
2. [Language prelude]

r[names.scopes.self]
## `Self` scope

r[names.scopes.self.intro]
Although [`Self`] is a keyword with special meaning, it interacts with name resolution in a way similar to normal names.

r[names.scopes.self.def-scope]
The implicit `Self` type in the definition of a [struct], [enum], [union], [trait], or [implementation] is treated similarly to a [generic parameter](#generic-parameter-scopes), and is in scope in the same way as a generic type parameter.

r[names.scopes.self.impl-scope]
The implicit `Self` constructor in the value [namespace] of an [implementation] is in scope within the body of the implementation (the implementation's [associated items]).

```rust
// Self type within struct definition.
struct Recursive {
    f1: Option<Box<Self>>
}

// Self value constructor within an implementation.
struct ImplExample();
impl ImplExample {
    fn example() -> Self { // Self type
        Self() // Self value constructor
    }
}
```

[`derive` attribute]: ../attributes/derive.md
[`for` loop]: ../expressions/loop-expr.md#iterator-loops
[`for`]: ../expressions/loop-expr.md#iterator-loops
[`if let`]: ../expressions/if-expr.md#if-let-patterns
[`while let`]: ../expressions/loop-expr.md#while-let-patterns
[`let` statement]: ../statements.md#let-statements
[`macro_export`]: ../macros-by-example.md#path-based-scope
[`macro_use` prelude]: preludes.md#macro_use-prelude
[`macro_use`]: ../macros-by-example.md#the-macro_use-attribute
[`match` arms]: ../expressions/match-expr.md
[`Self`]: ../paths.md#self-1
[Associated consts]: ../items/associated-items.md#associated-constants
[associated items]: ../items/associated-items.md
[Asterisk glob imports]: ../items/use-declarations.md
[async blocks]: ../expressions/block-expr.md#async-blocks
[call expressions]: ../expressions/call-expr.md
[Closure parameter]: ../expressions/closure-expr.md
[closures]: ../expressions/closure-expr.md
[const arguments]: ../items/generics.md#const-generics
[const contexts]: ../const_eval.md#const-context
[Const generic parameters]: ../items/generics.md#const-generics
[Const items]: ../items/constant-items.md
[Constant]: ../items/constant-items.md
[Derive macro helper attributes]: ../procedural-macros.md#derive-macro-helper-attributes
[destructors]: ../destructors.md
[entity]: ../names.md
[enum]: ../items/enumerations.mdr
[enums]: ../items/enumerations.md
[Extern prelude]: preludes.md#extern-prelude
[Function parameter]: ../items/functions.md#function-parameters
[hrtb]: ../trait-bounds.md#higher-ranked-trait-bounds
[Impl trait]: ../types/impl-trait.md
[implementation]: ../items/implementations.md
[items]: ../items.md
[Language prelude]: preludes.md#language-prelude
[loop expression]: ../expressions/loop-expr.md
[Loop labels]: ../expressions/loop-expr.md#loop-labels
[Macros By Example]: ../macros-by-example.md
[match guard]: ../expressions/match-expr.md#match-guards
[methods]: ../items/associated-items.md#methods
[module]: ../items/modules.md
[name resolution]: name-resolution.md
[namespace]: namespaces.md
[path]: ../paths.md
[pattern]: ../patterns.md
[placeholder lifetime]: ../lifetime-elision.md
[preludes]: preludes.md
[Standard library prelude]: preludes.md#standard-library-prelude
[statement]: ../statements.md
[Static items]: ../items/static-items.md
[static]: ../items/static-items.md
[struct]: ../items/structs.md
[structs]: ../items/structs.md
[Tool prelude]: preludes.md#tool-prelude
[trait]: ../items/traits.md
[union]: ../items/unions.md

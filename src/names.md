r[names]
# Names

r[names.intro]
An *entity* is a language construct that can be referred to in some way within
the source program, usually via a [path]. Entities include [types], [items],
[variable bindings] and [fields].

r[names.decl]
A *declaration* is a syntactical construct that can introduce a *name* to
refer to an entity. Entity names are valid within a [*scope*] --- a region of
source text where that name may be referenced.

r[names.explicit-decl]
Some entities are [explicitly declared](#explicitly-declared-entities) in the
source code, and some are [implicitly declared](#implicitly-declared-entities)
as part of the language or compiler extensions.

r[names.path]
[*Paths*] are used to refer to an entity, possibly in another module or type.

r[names.namespace]
Names are segregated into different [*namespaces*], allowing entities in
different namespaces to share the same name without conflict.

r[names.resolution]
[*Name resolution*] is the compile-time process of tying paths and identifiers to entity declarations.

r[names.visibility]
Access to certain names is not restricted without any visibility denotations, such as `pub`.

r[names.explicit]
## Explicitly declared entities

r[names.explicit.list]
Entities that explicitly introduce a name in the source code are:

r[names.explicit.item-decl]
* [Items]:
    * [Function declarations] and [function parameters]
    * [struct], [enum], enum variant declarations, and their named fields
    * [Constant item declarations]
    * [Trait item declarations] and their [associated items]
    * [Implementation] associated items

r[names.explicit.expr]
* [Expressions]:
    * [`while let`] pattern bindings
    * [`for`] pattern bindings
    * [`if let`] pattern bindings
    * [`match`] pattern bindings

r[names.explicit.binding]
* [`let` statement] pattern bindings

r[names.implicit]
## Implicitly declared entities

r[names.implicit.list]
The following entities are implicitly defined by the language, or are
introduced by compiler options and extensions:

r[names.implicit.primitive-types]
* [Language prelude]:
    * [Boolean type] --- `bool`
    * [Textual types] --- `char` and `str`
    * [Integer types] --- `i32`, `u32`
    * [Machine-dependent integer types] --- `usize` and `isize`

r[names.implicit.prelude]
* [Standard library prelude] items, attributes, and macros

r[names.implicit.stdlib]
* [Standard library][extern-prelude] crates in the root module

r[names.implicit.root]
Additionally, the crate root module does not have a name, but can be referred
to with certain [path qualifiers] or aliases.

[*Name resolution*]: names/name-resolution.md
[*namespaces*]: names/namespaces.md
[*paths*]: paths.md
[*scope*]: names/scopes.md
[*visibility*]: visibility-and-privacy.md
[`'static`]: keywords.md#weak-keywords
[`for`]: expressions/loop-expr.md#iterator-loops
[`if let`]: expressions/if-expr.md#if-let-patterns
[`let` statement]: statements.md#let-statements
[`macro_export` attribute]: macros-by-example.md#path-based-scope
[`macro_rules` declarations]: macros-by-example.md
[`macro_use` attribute]: macros-by-example.md#the-macro_use-attribute
[`match`]: expressions/match-expr.md
[`while let`]: expressions/loop-expr.md#while-let-patterns
[associated items]: items/associated-items.md
[attributes]: attributes.md
[Boolean type]: types/boolean.md
[Built-in attributes]: attributes.md#built-in-attributes-index
[Closure]: expressions/closure-expr.md
[Constant item declarations]: items/constant-items.md
[Derive helper attributes]: procedural-macros.md#derive-macro-helper-attributes
[enum]: items/enumerations.md
[Expressions]: expressions.md
[extern-prelude]: names/preludes.md#extern-prelude
[External block items]: items/external-blocks.md
[External crate declarations]: items/extern-crates.md
[fields]: expressions/field-expr.md
[floating-point types]: types/numeric.md#floating-point-types
[Function declarations]: items/functions.md
[function parameters]: items/functions.md#function-parameters
[Generic parameters]: items/generics.md
[Higher ranked trait bounds]: trait-bounds.md#higher-ranked-trait-bounds
[Implementation]: items/implementations.md
[Integer types]: types/numeric.md#integer-types
[Items]: items.md
[Language prelude]: names/preludes.md#language-prelude
[lifetimes-and-loop-labels]: tokens.md#lifetimes-and-loop-labels
[lifetimes]: tokens.md#lifetimes-and-loop-labels
[Lints]: attributes/diagnostics.md#lint-check-attributes
[Loop labels]: expressions/loop-expr.md#loop-labels
[Machine-dependent integer types]: types/numeric.md#machine-dependent-integer-types
[macro invocations]: macros.md#macro-invocation
[matcher metavariables]: macros-by-example.md#metavariables
[Module declarations]: items/modules.md
[path]: paths.md
[path qualifiers]: paths.md#path-qualifiers
[Standard library prelude]: names/preludes.md#standard-library-prelude
[Static item declarations]: items/static-items.md
[struct]: items/structs.md
[Textual types]: types/textual.md
[Tool attributes]: attributes.md#tool-attributes
[tool lint attributes]: attributes/diagnostics.md#tool-lint-attributes
[Trait item declarations]: items/traits.md
[Type aliases]: items/type-aliases.md
[types]: types.md
[union]: items/unions.md
[Use declarations]: items/use-declarations.md
[variable bindings]: patterns.md

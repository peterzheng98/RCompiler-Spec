r[items.fn]
# Functions

r[items.fn.syntax]
```grammar,items
Function ->
    FunctionQualifiers `fn` IDENTIFIER
        `(` FunctionParameters? `)`
        FunctionReturnType?
        ( BlockExpression | `;` )

FunctionQualifiers -> `const`? 

FunctionParameters ->
      SelfParam `,`?
    | (SelfParam `,`)? FunctionParam (`,` FunctionParam)* `,`?

SelfParam -> ( ShorthandSelf | TypedSelf )

ShorthandSelf -> `&`? `mut`? `self`

TypedSelf -> `mut`? `self` `:` Type

FunctionParam -> PatternNoTopAlt `:` Type

FunctionReturnType -> `->` Type
```

[^async-edition]: The `async` qualifier is not allowed in the 2015 edition.

[^extern-safe]: The `safe` function qualifier is only allowed semantically within
  `extern` blocks.

[^extern-qualifiers]: *Relevant to editions earlier than Rust 2024*: Within
  `extern` blocks, the `safe` or `unsafe` function qualifier is only allowed
  when the `extern` is qualified as `unsafe`.

[^fn-param-2015]: Function parameters with only a type are only allowed
  in an associated function of a [trait item] in the 2015 edition.

r[items.fn.intro]
A _function_ consists of a [block] (that's the _body_ of the function),
along with a name, a set of parameters, and an output type.
Other than a name, all these are optional.

r[items.fn.namespace]
Functions are declared with the keyword `fn` which defines the given name in the [value namespace] of the module or block where it is located.

r[items.fn.signature]
Functions may declare a set of *input* [*variables*][variables] as parameters, through which the caller passes arguments into the function, and the *output* [*type*][type] of the value the function will return to its caller on completion.

r[items.fn.implicit-return]
If the output type is not explicitly stated, it is the [unit type].

r[items.fn.fn-item-type]
When referred to, a _function_ yields a first-class *value* of the corresponding zero-sized [*function item type*], which when called evaluates to a direct call to the function.

For example, this is a simple function:
```rust
fn answer_to_life_the_universe_and_everything() -> i32 {
    return 42;
}
```

r[items.fn.safety-qualifiers]
The `safe` function is semantically only allowed when used in an [`extern` block].

r[items.fn.params]
## Function parameters

r[items.fn.params.intro]
Function parameters are irrefutable [patterns], so any pattern that is valid in
an else-less `let` binding is also valid as a parameter:

```rust
fn first((value, _): (i32, i32)) -> i32 { value }
```

r[items.fn.params.self-pat]
If the first parameter is a [SelfParam], this indicates that the function is a
[method].

r[items.fn.params.self-restriction]
Functions with a self parameter may only appear as an [associated
function] in a [trait] or [implementation].

r[items.fn.params.varargs]
A parameter with the `...` token indicates a [variadic function], and may only
be used as the last parameter of an [external block] function. The variadic
parameter may have an optional identifier, such as `args: ...`.

r[items.fn.body]
## Function body

r[items.fn.body.intro]
The body block of a function is conceptually wrapped in another block that first binds the
argument patterns and then `return`s the value of the function's body. This
means that the tail expression of the block, if evaluated, ends up being
returned to the caller. As usual, an explicit return expression within
the body of the function will short-cut that implicit return, if reached.

For example, the function above behaves as if it was written as:

<!-- ignore: example expansion -->
```rust,ignore
// argument_0 is the actual first argument passed from the caller
let (value, _) = argument_0;
return {
    value
};
```

r[items.fn.body.bodyless]
Functions without a body block are terminated with a semicolon. This form
may only appear in a [trait] or [external block].


r[items.fn.const]
## Const functions

r[items.fn.const.intro]
Functions qualified with the `const` keyword are [const functions]. In addition, constructors of tuple-like enum variants are also considered const functions and can be called from within [const contexts].

r[items.fn.const.extern]
Const functions may use the [`extern`] function qualifier.

r[items.fn.const.exclusivity]
Const functions are not allowed to be [async](#async-functions).

[const contexts]: ../const_eval.md#const-context
[const functions]: ../const_eval.md#const-functions
[tuple struct]: structs.md
[tuple variant]: enumerations.md
[`extern`]: #extern-function-qualifier
[external block]: external-blocks.md
[path]: ../paths.md
[block]: ../expressions/block-expr.md
[variables]: ../variables.md
[type]: ../types.md#type-expressions
[unit type]: ../types/tuple.md
[*function item type*]: ../types/function-item.md
[Trait]: traits.md
[attributes]: ../attributes.md
[`cfg`]: ../conditional-compilation.md#the-cfg-attribute
[`cfg_attr`]: ../conditional-compilation.md#the-cfg_attr-attribute
[the lint check attributes]: ../attributes/diagnostics.md#lint-check-attributes
[the procedural macro attributes]: ../procedural-macros.md
[the testing attributes]: ../attributes/testing.md
[the optimization hint attributes]: ../attributes/codegen.md#optimization-hints
[`deprecated`]: ../attributes/diagnostics.md#the-deprecated-attribute
[`doc`]: ../../rustdoc/the-doc-attribute.html
[`must_use`]: ../attributes/diagnostics.md#the-must_use-attribute
[patterns]: ../patterns.md
[`export_name`]: ../abi.md#the-export_name-attribute
[`link_section`]: ../abi.md#the-link_section-attribute
[`no_mangle`]: ../abi.md#the-no_mangle-attribute
[built-in attributes]: ../attributes.md#built-in-attributes-index
[trait item]: traits.md
[method]: associated-items.md#methods
[associated function]: associated-items.md#associated-functions-and-methods
[implementation]: implementations.md
[value namespace]: ../names/namespaces.md
[variadic function]: external-blocks.md#variadic-functions
[`extern` block]: external-blocks.md

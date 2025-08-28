r[const-eval]
# Constant evaluation

r[const-eval.general]
Constant evaluation is the process of computing the result of
[expressions] during compilation. Only a subset of all expressions
can be evaluated at compile-time.

r[const-eval.const-expr]
## Constant expressions

r[const-eval.const-expr.general]
Certain forms of expressions, called constant expressions, can be evaluated at
compile time.

r[const-eval.const-expr.const-context]
In [const contexts](#const-context), these are the only allowed
expressions, and are always evaluated at compile time.

r[const-eval.const-expr.runtime-context]
In other places, such as [let statements], constant expressions *may* be, but are not guaranteed to be, evaluated at compile time.

r[const-eval.const-expr.error]
Behaviors such as out of bounds [array indexing] or [overflow] are compiler errors if the value
must be evaluated at compile time (i.e. in const contexts). Otherwise, these
behaviors are warnings, but will likely panic at run-time.

r[const-eval.const-expr.list]
The following expressions are constant expressions, so long as any operands are also constant expressions.

r[const-eval.const-expr.literal]
* [Literals].

r[const-eval.const-expr.path-item]
* [Paths] to [functions] and [constants].
  Recursively defining constants is not allowed.

r[const-eval.const-expr.array]
* [Array expressions].

r[const-eval.const-expr.constructor]
* [Struct] expressions.

r[const-eval.const-expr.field]
* [Field] expressions.

r[const-eval.const-expr.index]
* Index expressions, [array indexing] or [slice] with a `usize`.

r[const-eval.const-expr.builtin-arith-logic]
* Built-in [negation], [arithmetic], [logical], [comparison] or [lazy boolean]
  operators used on integer types, `bool`, and `char`.

r[const-eval.const-expr.group]
* [Grouped] expressions.

r[const-eval.const-expr.cast]
* [Cast] expressions.

r[const-eval.const-expr.if]
* [if] expressions.

The following expressions are *not* considered in constant expressions:

r[const-eval.const-expr.block]
* [Block expressions]
    * [let statements] and thus irrefutable [patterns], including mutable bindings
    * [assignment expressions]
    * [compound assignment expressions]
    * [expression statements]

r[const-eval.const-expr.func]
* Const functions.

r[const-eval.const-expr.loop]
* [loop] and [while] expressions.

r[const-eval.const-expr.borrows]
* All forms of [borrow]s.

r[const-eval.const-expr.deref]
* [Dereference] expressions.

r[const-eval.const-context]
## Const context
[const context]: #const-context

r[const-eval.const-context.general]
A _const context_ is one of the following:

r[const-eval.const-context.array-length]
* [Array type length expressions]

r[const-eval.const-context.repeat-length]
* [Array repeat length expressions][array expressions]

r[const-eval.const-context.init]
* The initializer of
  * [constants]
  * [enum variants]

Const contexts that are used as parts of types (array type and repeat length
expressions as well as const generic arguments) can only make restricted use of
surrounding generic parameters: such an expression must either be a single bare
const generic parameter, or an arbitrary expression not making use of any
generics.

r[const-eval.const-evaluation]
## Const evaluation

We ensure that all usage of other constant items is defined before they are used. As a result, it is not necessary to build a dependency graph of constant items to evaluate them in the correct order. Scanning the AST suffices to evaluate all constant items.

[arithmetic]:           expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[array expressions]:    expressions/array-expr.md
[array indexing]:       expressions/array-expr.md#array-and-slice-indexing-expressions
[array indexing]:       expressions/array-expr.md#array-and-slice-indexing-expressions
[array type length expressions]: types/array.md
[assignment expressions]: expressions/operator-expr.md#assignment-expressions
[compound assignment expressions]: expressions/operator-expr.md#compound-assignment-expressions
[block expressions]:    expressions/block-expr.md
[borrow]:               expressions/operator-expr.md#borrow-operators
[cast]:                 expressions/operator-expr.md#type-cast-expressions
[closure expressions]:  expressions/closure-expr.md
[comparison]:           expressions/operator-expr.md#comparison-operators
[const block]:          expressions/block-expr.md#const-blocks
[const functions]:      items/functions.md#const-functions
[const generic argument]: items/generics.md#const-generics
[const generic parameters]: items/generics.md#const-generics
[constants]:            items/constant-items.md
[Const parameters]:     items/generics.md
[Dereference]:         expressions/operator-expr.md#the-dereference-operator
[dereference expression]: expressions/operator-expr.md#the-dereference-operator
[dereference operator]: expressions/operator-expr.md#the-dereference-operator
[destructors]:          destructors.md
[enum variants]:   items/enumerations.md
[expression statements]: statements.md#expression-statements
[expressions]:          expressions.md
[`extern` statics]:     items/external-blocks.md#statics
[field]:                expressions/field-expr.md
[functions]:            items/functions.md
[grouped]:              expressions/grouped-expr.md
[interior mutability]:  interior-mutability.md
[if]:                   expressions/if-expr.md#if-expressions
[lazy boolean]:         expressions/operator-expr.md#lazy-boolean-operators
[let statements]:       statements.md#let-statements
[literals]:             expressions/literal-expr.md
[logical]:              expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[loop]:                 expressions/loop-expr.md#infinite-loops
[match]:                expressions/match-expr.md
[negation]:             expressions/operator-expr.md#negation-operators
[overflow]:             expressions/operator-expr.md#overflow
[paths]:                expressions/path-expr.md
[patterns]:             patterns.md
[place expression]:     expr.place-value.place-memory-location
[promoted expression]:  destructors.md#constant-promotion
[promoted]:             destructors.md#constant-promotion
[range expressions]:    expressions/range-expr.md
[slice]:                types/slice.md
[statics]:              items/static-items.md
[struct]:               expressions/struct-expr.md
[temporary lifetime extension]: destructors.scope.lifetime-extension
[tuple expressions]:    expressions/tuple-expr.md
[while]:                expressions/loop-expr.md#predicate-loops

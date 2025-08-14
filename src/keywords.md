r[lex.keywords]
# Keywords

Rust divides keywords into three categories:

- [Keywords](#keywords)
  - [Strict keywords](#strict-keywords)

r[lex.keywords.strict]
## Strict keywords

r[lex.keywords.strict.intro]
These keywords can only be used in their correct contexts. They cannot
be used as the names of:

* [Items]
* [Variables] and function parameters
* Fields and [variants]
* [Type parameters]
* Lifetime parameters or [loop labels]
* [Crates]

r[lex.keywords.strict.list]
The following keywords are in all editions:

- `as`
- `break`
- `const`
- `continue`
- `crate`
- `else`
- `enum`
- `false`
- `fn`
- `for`
- `if`
- `impl`
- `in`
- `let`
- `loop`
- `match`
- `mod`
- `move`
- `mut`
- `pub`, you do not need to implement this
- `ref`
- `return`
- `self`
- `Self`
- `static`
- `struct`
- `super`
- `trait`
- `true`
- `type`
- `unsafe`
- `use`
- `where`
- `while`

r[lex.keywords.strict.edition2018]
The following keyword was added beginning in the 2018 edition.

- `dyn`
<!-- 
r[lex.keywords.reserved]
## Reserved keywords

r[lex.keywords.reserved.intro]
These keywords aren't used yet, but they are reserved for future use. They have
the same restrictions as strict keywords. The reasoning behind this is to make
current programs forward compatible with future versions of Rust by forbidding
them to use these keywords.

r[lex.keywords.reserved.list]
- `abstract`
- `become`
- `box`
- `do`
- `final`
- `macro`
- `override`
- `priv`
- `typeof`
- `unsized`
- `virtual`
- `yield`

r[lex.keywords.reserved.edition2018]
The following keywords are reserved beginning in the 2018 edition.

- `try`

r[lex.keywords.reserved.edition2024]
The following keywords are reserved beginning in the 2024 edition.

- `gen` -->

[items]: items.md
[Variables]: variables.md
[Type parameters]: types/parameters.md
[loop labels]: expressions/loop-expr.md#loop-labels
[Macros]: macros.md
[attributes]: attributes.md
[Macro placeholders]: macros-by-example.md
[Crates]: crates-and-source-files.md
[union]: items/unions.md
[variants]: items/enumerations.md
[`dyn`]: types/trait-object.md
[loop label]: expressions/loop-expr.md#loop-labels
[generic lifetime parameter]: items/generics.md
[external blocks]: items/external-blocks.md
[raw borrow operators]: expressions/operator-expr.md#raw-borrow-operators

r[ident]
# Identifiers

r[ident.syntax]
```grammar,lexer
IDENTIFIER_OR_KEYWORD ->
      ASCII_ALPHA (ASCII_ALPHA | ASCII_DIGIT | `_`)*

ASCII_ALPHA -> [`a`-`z` `A`-`Z`]

ASCII_DIGIT -> [`0`-`9`]

RAW_IDENTIFIER -> `r#` IDENTIFIER_OR_KEYWORD _except `crate`, `self`, `super`, `Self`_

NON_KEYWORD_IDENTIFIER -> IDENTIFIER_OR_KEYWORD _except a [strict][lex.keywords.strict] or [reserved][lex.keywords.reserved] keyword_

IDENTIFIER -> NON_KEYWORD_IDENTIFIER | RAW_IDENTIFIER

RESERVED_RAW_IDENTIFIER -> `r#_`
```

<!-- When updating the version, update the UAX links, too. -->
r[ident.ascii]
Identifiers are restricted to ASCII characters only. The first character must be an English letter (a-z, A-Z), and subsequent characters can be English letters, digits (0-9), or underscores. Some examples of identifiers:

* `foo`
* `identifier`
* `r#true`
* `myVariable123`
* `test_case`

r[ident.length]
Identifiers are case-sensitive. Identifiers longer than 64 characters result in undefined behavior.

r[ident.profile]
The profile used is:

* Start := ASCII letters (`a`-`z`, `A`-`Z`)
* Continue := ASCII letters (`a`-`z`, `A`-`Z`), ASCII digits (`0`-`9`), and underscore (`_`)

> [!NOTE]
> Unlike standard Rust, identifiers starting with an underscore are not allowed in this specification.

> [!WARNING]
> Unicode identifiers (such as `Москва`, `東京`) are not supported and result in undefined behavior.

r[ident.keyword]
Identifiers may not be a [strict] or [reserved] keyword without the `r#` prefix described below in [raw identifiers](#raw-identifiers).

r[ident.restrictions]
## Restrictions

r[ident.restrictions.ascii-only]
All identifiers must use ASCII characters only. Unicode characters are not supported and result in undefined behavior.

r[ident.restrictions.length]
Identifiers longer than 64 characters result in undefined behavior.

r[ident.restrictions.underscore]
Identifiers may not start with an underscore character. This differs from standard Rust behavior.

r[ident.normalization]
## Normalization

Since identifiers are restricted to ASCII characters, no Unicode normalization is required. Identifiers are compared byte-for-byte and are case-sensitive.

r[ident.raw]
## Raw identifiers

r[ident.raw.intro]
A raw identifier is like a normal identifier, but prefixed by `r#`. (Note that
the `r#` prefix is not included as part of the actual identifier.)

r[ident.raw.allowed]
Unlike a normal identifier, a raw identifier may be any strict or reserved
keyword except the ones listed above for `RAW_IDENTIFIER`. Raw identifiers must still follow the ASCII character restrictions and 64-character length limit.

r[ident.raw.reserved]
It is an error to use the [RESERVED_RAW_IDENTIFIER] token `r#_` in order to avoid confusion with the [WildcardPattern].

[`extern crate`]: items/extern-crates.md
[`no_mangle`]: abi.md#the-no_mangle-attribute
[`path` attribute]: items/modules.md#the-path-attribute
[external blocks]: items/external-blocks.md
[module]: items/modules.md
[path]: paths.md
[reserved]: keywords.md#reserved-keywords
[strict]: keywords.md#strict-keywords

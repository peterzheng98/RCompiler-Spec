r[lex.token]
# Tokens

r[lex.token.syntax]
```grammar,lexer
Token ->
      IDENTIFIER_OR_KEYWORD
    | CHAR_LITERAL
    | STRING_LITERAL
    | RAW_STRING_LITERAL
    | C_STRING_LITERAL
    | RAW_C_STRING_LITERAL
    | INTEGER_LITERAL
    | PUNCTUATION
```

r[lex.token.intro]
Tokens are primitive productions in the grammar defined by regular
(non-recursive) languages.  Rust source input can be broken down
into the following kinds of tokens:

* [Keywords]
* [Identifiers][identifier]
* [Literals](#literals)
* [Punctuation](#punctuation)
* [Delimiters](#delimiters)

Within this documentation's grammar, "simple" tokens are given in [string
table production] form, and appear in `monospace` font.

[string table production]: notation.md#string-table-productions

r[lex.token.literal]
## Literals

Literals are tokens used in [literal expressions].

### Examples

#### Characters and strings

|                                              | Example         | `#`&nbsp;sets[^nsets] | Characters  | Escapes             |
|----------------------------------------------|-----------------|------------|-------------|---------------------|
| [Character](#character-literals)             | `'H'`           | 0          | All ASCII   | [Quote](#quote-escapes) & [ASCII](#ascii-escapes) |
| [String](#string-literals)                   | `"hello"`       | 0          | All ASCII   | [Quote](#quote-escapes) & [ASCII](#ascii-escapes) |
| [Raw string](#raw-string-literals)           | `r#"hello"#`    | <256       | All ASCII   | `N/A`                                                      |
| [C string](#c-string-literals)               | `c"hello"`      | 0          | All ASCII   | [Quote](#quote-escapes) & [Byte](#byte-escapes) |
| [Raw C string](#raw-c-string-literals)       | `cr#"hello"#`   | <256       | All ASCII   | `N/A`                                                                           |

[^nsets]: The number of `#`s on each side of the same literal must be equivalent.


#### ASCII escapes

|   | Name |
|---|------|
| `\x41` | 7-bit character code (exactly 2 digits, up to 0x7F) |
| `\n` | Newline |
| `\r` | Carriage return |
| `\t` | Tab |
| `\\` | Backslash |
| `\0` | Null |

#### Quote escapes

|   | Name |
|---|------|
| `\'` | Single quote |
| `\"` | Double quote |

#### Numbers

| [Number literals](#number-literals)[^nl] | Example | Exponentiation |
|----------------------------------------|---------|----------------|
| Decimal integer | `98_222` | `N/A` |
| Hex integer | `0xff` | `N/A` |
| Octal integer | `0o77` | `N/A` |
| Binary integer | `0b1111_0000` | `N/A` |

[^nl]: All number literals allow `_` as a visual separator: `1_234.0f64`

r[lex.token.literal.suffix]
#### Suffixes

r[lex.token.literal.literal.suffix.intro]
A suffix is a sequence of characters following the primary part of a literal (without intervening whitespace), of the same form as a identifier or keyword.

r[lex.token.literal.suffix.syntax]
```grammar,lexer
SUFFIX -> IDENTIFIER_OR_KEYWORD

SUFFIX_NO_E -> SUFFIX _not beginning with `e` or `E`_
```

<!-- r[lex.token.literal.suffix.validity]
Any kind of literal (string, integer, etc) with any suffix is valid as a token.

A literal token with any suffix can be passed to a macro without producing an error.
The macro itself will decide how to interpret such a token and whether to produce an error or not.
In particular, the `literal` fragment specifier for by-example macros matches literal tokens with arbitrary suffixes.

```rust
macro_rules! blackhole { ($tt:tt) => () }
macro_rules! blackhole_lit { ($l:literal) => () }

blackhole!("string"suffix); // OK
blackhole_lit!(1suffix); // OK
``` -->

r[lex.token.literal.suffix.parse]
Suffixes on literal tokens which are interpreted as literal expressions or patterns are restricted.
Any suffixes are rejected on non-numeric literal tokens,
and numeric literal tokens are accepted only with suffixes from the list below.

| Integer |
|---------|
| `u32`, `i32`, `usize`, `isize` | 

### Character and string literals

r[lex.token.literal.char]
#### Character literals

r[lex.token.literal.char.syntax]
```grammar,lexer
CHAR_LITERAL ->
    `'`
        ( ~[`'` `\` LF CR TAB] | QUOTE_ESCAPE | ASCII_ESCAPE )
    `'` SUFFIX?

QUOTE_ESCAPE -> `\'` | `\"`

ASCII_ESCAPE ->
      `\x` OCT_DIGIT HEX_DIGIT
    | `\n` | `\r` | `\t` | `\\` | `\0`
```

r[lex.token.literal.char.intro]
A _character literal_ is a single ASCII character enclosed within two
`U+0027` (single-quote) characters, with the exception of `U+0027` itself,
which must be _escaped_ by a preceding `U+005C` character (`\`).

r[lex.token.literal.str]
#### String literals

r[lex.token.literal.str.syntax]
```grammar,lexer
STRING_LITERAL ->
    `"` (
        ~[`"` `\` CR]
      | QUOTE_ESCAPE
      | ASCII_ESCAPE
      | STRING_CONTINUE
    )* `"` SUFFIX?

STRING_CONTINUE -> `\` LF
```

r[lex.token.literal.str.intro]
A _string literal_ is a sequence of any ASCII characters enclosed within two
`U+0022` (double-quote) characters, with the exception of `U+0022` itself,
which must be _escaped_ by a preceding `U+005C` character (`\`).

r[lex.token.literal.str.linefeed]
Line-breaks, represented by the  character `U+000A` (LF), are allowed in string literals.
The character `U+000D` (CR) may not appear in a string literal.
When an unescaped `U+005C` character (`\`) occurs immediately before a line break, the line break does not appear in the string represented by the token.
See [String continuation escapes] for details.

r[lex.token.literal.char-escape]
#### Character escapes

r[lex.token.literal.char-escape.intro]
Some additional _escapes_ are available in either character or non-raw string
literals. An escape starts with a `U+005C` (`\`) and continues with one of the
following forms:

r[lex.token.literal.char-escape.ascii]
* A _7-bit code point escape_ starts with `U+0078` (`x`) and is
  followed by exactly two _hex digits_ with value up to `0x7F`. It denotes the
  ASCII character with value equal to the provided hex value.

r[lex.token.literal.char-escape.whitespace]
* A _whitespace escape_ is one of the characters `U+006E` (`n`), `U+0072`
  (`r`), or `U+0074` (`t`), denoting the Unicode values `U+000A` (LF),
  `U+000D` (CR) or `U+0009` (HT) respectively.

r[lex.token.literal.char-escape.null]
* The _null escape_ is the character `U+0030` (`0`) and denotes the Unicode
  value `U+0000` (NUL).

r[lex.token.literal.char-escape.slash]
* The _backslash escape_ is the character `U+005C` (`\`) which must be
  escaped in order to denote itself.

r[lex.token.literal.str-raw]
#### Raw string literals

r[lex.token.literal.str-raw.syntax]
```grammar,lexer
RAW_STRING_LITERAL -> `r` RAW_STRING_CONTENT SUFFIX?

RAW_STRING_CONTENT ->
      `"` ( ~CR )*? `"`
    | `#` RAW_STRING_CONTENT `#`
```

r[lex.token.literal.str-raw.intro]
Raw string literals do not process any escapes. They start with the character
`U+0072` (`r`), followed by fewer than 256 of the character `U+0023` (`#`) and a
`U+0022` (double-quote) character.

r[lex.token.literal.str-raw.body]
The _raw string body_ can contain any sequence of ASCII characters other than `U+000D` (CR).
It is terminated only by another `U+0022` (double-quote) character, followed by the same number of `U+0023` (`#`) characters that preceded the opening `U+0022` (double-quote) character.

r[lex.token.literal.str-raw.content]
All ASCII characters contained in the raw string body represent themselves,
the characters `U+0022` (double-quote) (except when followed by at least as
many `U+0023` (`#`) characters as were used to start the raw string literal) or
`U+005C` (`\`) do not have any special meaning.

Examples for string literals:

```rust
"foo"; r"foo";                     // foo
"\"foo\""; r#""foo""#;             // "foo"

"foo #\"# bar";
r##"foo #"# bar"##;                // foo #"# bar

"\x52"; "R"; r"R";                 // R
"\\x52"; r"\x52";                  // \x52
```

### C string and raw C string literals

r[lex.token.str-c]
#### C string literals

r[lex.token.str-c.syntax]
```grammar,lexer
C_STRING_LITERAL ->
    `c"` (
        ~[`"` `\` CR NUL]
      | BYTE_ESCAPE _except `\0` or `\x00`_
      | STRING_CONTINUE
    )* `"` SUFFIX?

```

r[lex.token.str-c.intro]
A _C string literal_ is a sequence of ASCII characters and _escapes_,
preceded by the characters `U+0063` (`c`) and `U+0022` (double-quote), and
followed by the character `U+0022`. If the character `U+0022` is present within
the literal, it must be _escaped_ by a preceding `U+005C` (`\`) character.
Alternatively, a C string literal can be a _raw C string literal_, defined below.

[CStr]: core::ffi::CStr

r[lex.token.str-c.null]
C strings are implicitly terminated by byte `0x00`, so the C string literal
`c""` is equivalent to manually constructing a `&CStr` from the byte string
literal `b"\x00"`. Other than the implicit terminator, byte `0x00` is not
permitted within a C string.

r[lex.token.str-c.linefeed]
Line-breaks, represented by the  character `U+000A` (LF), are allowed in C string literals.
The character `U+000D` (CR) may not appear in a C string literal.
When an unescaped `U+005C` character (`\`) occurs immediately before a line break, the line break does not appear in the string represented by the token.
See [String continuation escapes] for details.

r[lex.token.str-c.escape]
Some additional _escapes_ are available in non-raw C string literals. An escape
starts with a `U+005C` (`\`) and continues with one of the following forms:

r[lex.token.str-c.escape-byte]
* A _byte escape_ escape starts with `U+0078` (`x`) and is followed by exactly
  two _hex digits_ with value up to `0x7F`. It denotes the byte equal to the provided hex value.

r[lex.token.str-c.escape-whitespace]
* A _whitespace escape_ is one of the characters `U+006E` (`n`), `U+0072`
  (`r`), or `U+0074` (`t`), denoting the bytes values `0x0A` (ASCII LF),
  `0x0D` (ASCII CR) or `0x09` (ASCII HT) respectively.

r[lex.token.str-c.escape-slash]
* The _backslash escape_ is the character `U+005C` (`\`) which must be
  escaped in order to denote its ASCII encoding `0x5C`.

r[lex.token.str-c.edition2021]
> [!EDITION-2021]
> C string literals are accepted in the 2021 edition or later. In earlier additions the token `c""` is lexed as `c ""`.

r[lex.token.str-c-raw]
#### Raw C string literals

r[lex.token.str-c-raw.syntax]
```grammar,lexer
RAW_C_STRING_LITERAL ->
    `cr` RAW_C_STRING_CONTENT SUFFIX?

RAW_C_STRING_CONTENT ->
      `"` ( ~[CR NUL] )*? `"`
    | `#` RAW_C_STRING_CONTENT `#`
```

r[lex.token.str-c-raw.intro]
Raw C string literals do not process any escapes. They start with the
character `U+0063` (`c`), followed by `U+0072` (`r`), followed by fewer than 256
of the character `U+0023` (`#`), and a `U+0022` (double-quote) character.

r[lex.token.str-c-raw.body]
The _raw C string body_ can contain any sequence of ASCII characters other than `U+0000` (NUL) and `U+000D` (CR).
It is terminated only by another `U+0022` (double-quote) character, followed by the same number of `U+0023` (`#`) characters that preceded the opening `U+0022` (double-quote) character.

r[lex.token.str-c-raw.content]
All characters contained in the raw C string body represent themselves in ASCII
encoding. The characters `U+0022` (double-quote) (except when followed by at
least as many `U+0023` (`#`) characters as were used to start the raw C string
literal) or `U+005C` (`\`) do not have any special meaning.

r[lex.token.str-c-raw.edition2021]
> [!EDITION-2021]
> Raw C string literals are accepted in the 2021 edition or later. In earlier additions the token `cr""` is lexed as `cr ""`, and `cr#""#` is lexed as `cr #""#` (which is non-grammatical).

#### Examples for C string and raw C string literals

```rust
c"foo"; cr"foo";                     // foo
c"\"foo\""; cr#""foo""#;             // "foo"

c"foo #\"# bar";
cr##"foo #"# bar"##;                 // foo #"# bar

c"\x52"; c"R"; cr"R";                // R
c"\\x52"; cr"\x52";                  // \x52
```

r[lex.token.literal.num]
### Number literals

A _number literal_ is either an _integer literal_ or a _floating-point
literal_. The grammar for recognizing the two kinds of literals is mixed.

r[lex.token.literal.int]
#### Integer literals

r[lex.token.literal.int.syntax]
```grammar,lexer
INTEGER_LITERAL ->
    ( DEC_LITERAL | BIN_LITERAL | OCT_LITERAL | HEX_LITERAL ) SUFFIX_NO_E?

DEC_LITERAL -> DEC_DIGIT (DEC_DIGIT|`_`)*

BIN_LITERAL -> `0b` (BIN_DIGIT|`_`)* BIN_DIGIT (BIN_DIGIT|`_`)*

OCT_LITERAL -> `0o` (OCT_DIGIT|`_`)* OCT_DIGIT (OCT_DIGIT|`_`)*

HEX_LITERAL -> `0x` (HEX_DIGIT|`_`)* HEX_DIGIT (HEX_DIGIT|`_`)*

BIN_DIGIT -> [`0`-`1`]

OCT_DIGIT -> [`0`-`7`]

DEC_DIGIT -> [`0`-`9`]

HEX_DIGIT -> [`0`-`9` `a`-`f` `A`-`F`]
```

r[lex.token.literal.int.kind]
An _integer literal_ has one of four forms:

r[lex.token.literal.int.kind-dec]
* A _decimal literal_ starts with a *decimal digit* and continues with any
  mixture of *decimal digits* and _underscores_.

r[lex.token.literal.int.kind-hex]
* A _hex literal_ starts with the character sequence `U+0030` `U+0078`
  (`0x`) and continues as any mixture (with at least one digit) of hex digits
  and underscores.

r[lex.token.literal.int.kind-oct]
* An _octal literal_ starts with the character sequence `U+0030` `U+006F`
  (`0o`) and continues as any mixture (with at least one digit) of octal digits
  and underscores.

r[lex.token.literal.int.kind-bin]
* A _binary literal_ starts with the character sequence `U+0030` `U+0062`
  (`0b`) and continues as any mixture (with at least one digit) of binary digits
  and underscores.

r[lex.token.literal.int.restriction]
Like any literal, an integer literal may be followed (immediately, without any spaces) by a suffix as described above.
See [Integer literal expressions] for the effect of these suffixes.

Examples of integer literals which are accepted as literal expressions:

```rust
# #![allow(overflowing_literals)]
123;
123i32;
123u32;
123_u32;

0xff;
0xff_u8;
0x01_f32; // integer 7986, not floating-point 1.0

0o70;
0o70_i16;

0b1111_1111_1001_0000;
0b1111_1111_1001_0000i64;
0b________1;

0usize;

// These are too big for their type, but are accepted as literal expressions.
128_i8;
256_u8;

// This is an integer literal, accepted as a floating-point literal expression.
5f32;
```

Note that `-1i8`, for example, is analyzed as two tokens: `-` followed by `1i8`.

Examples of integer literals which are not accepted as literal expressions:

```rust
# #[cfg(false)] {
0invalidSuffix;
123AFB43;
0b010a;
0xAB_CD_EF_GH;
0b1111_f32;
# }
```

r[lex.token.punct]
## Punctuation

r[lex.token.punct.syntax]
```grammar,lexer
PUNCTUATION ->
      `=`
    | `<`
    | `<=`
    | `==`
    | `!=`
    | `>=`
    | `>`
    | `&&`
    | `||`
    | `!`
    | `~`
    | `+`
    | `-`
    | `*`
    | `/`
    | `%`
    | `^`
    | `&`
    | `|`
    | `<<`
    | `>>`
    | `+=`
    | `-=`
    | `*=`
    | `/=`
    | `%=`
    | `^=`
    | `&=`
    | `|=`
    | `<<=`
    | `>>=`
    | `@`
    | `.`
    | `..`
    | `...`
    | `..=`
    | `,`
    | `;`
    | `:`
    | `::`
    | `->`
    | `<-`
    | `=>`
    | `#`
    | `$`
    | `?`
    | `_`
    | `{`
    | `}`
    | `[`
    | `]`
    | `(`
    | `)`
```

r[lex.token.punct.intro]
Punctuation symbol tokens are listed here for completeness. Their individual
usages and meanings are defined in the linked pages.

| Symbol | Name        | Usage |
|--------|-------------|-------|
| `+`    | Plus        | [Addition][arith], [Trait Bounds], [Macro Kleene Matcher][macros]
| `-`    | Minus       | [Subtraction][arith], [Negation]
| `*`    | Star        | [Multiplication][arith], [Dereference], [Raw Pointers], [Macro Kleene Matcher][macros], [Use wildcards]
| `/`    | Slash       | [Division][arith]
| `%`    | Percent     | [Remainder][arith]
| `^`    | Caret       | [Bitwise and Logical XOR][arith]
| `!`    | Not         | [Bitwise and Logical NOT][negation], [Macro Calls][macros], [Inner Attributes][attributes], [Never Type], [Negative impls]
| `&`    | And         | [Bitwise and Logical AND][arith], [Borrow], [References], [Reference patterns]
| <code>\|</code> | Or | [Bitwise and Logical OR][arith], [Closures], Patterns in [match], [if let], and [while let]
| `&&`   | AndAnd      | [Lazy AND][lazy-bool], [Borrow], [References], [Reference patterns]
| <code>\|\|</code> | OrOr | [Lazy OR][lazy-bool], [Closures]
| `<<`   | Shl         | [Shift Left][arith], [Nested Generics][generics]
| `>>`   | Shr         | [Shift Right][arith], [Nested Generics][generics]
| `+=`   | PlusEq      | [Addition assignment][compound]
| `-=`   | MinusEq     | [Subtraction assignment][compound]
| `*=`   | StarEq      | [Multiplication assignment][compound]
| `/=`   | SlashEq     | [Division assignment][compound]
| `%=`   | PercentEq   | [Remainder assignment][compound]
| `^=`   | CaretEq     | [Bitwise XOR assignment][compound]
| `&=`   | AndEq       | [Bitwise And assignment][compound]
| <code>\|=</code> | OrEq | [Bitwise Or assignment][compound]
| `<<=`  | ShlEq       | [Shift Left assignment][compound]
| `>>=`  | ShrEq       | [Shift Right assignment][compound], [Nested Generics][generics]
| `=`    | Eq          | [Assignment], [Attributes], Various type definitions
| `==`   | EqEq        | [Equal][comparison]
| `!=`   | Ne          | [Not Equal][comparison]
| `>`    | Gt          | [Greater than][comparison], [Generics], [Paths]
| `<`    | Lt          | [Less than][comparison], [Generics], [Paths]
| `>=`   | Ge          | [Greater than or equal to][comparison], [Generics]
| `<=`   | Le          | [Less than or equal to][comparison]
| `@`    | At          | [Subpattern binding]
| `_`    | Underscore  | [Wildcard patterns], [Inferred types], Unnamed items in [constants], [extern crates], [use declarations], and [destructuring assignment]
| `.`    | Dot         | [Field access][field], [Tuple index]
| `..`   | DotDot      | [Range][range], [Struct expressions], [Patterns], [Range Patterns][rangepat]
| `...`  | DotDotDot   | [Variadic functions][extern], [Range patterns]
| `..=`  | DotDotEq    | [Inclusive Range][range], [Range patterns]
| `,`    | Comma       | Various separators
| `;`    | Semi        | Terminator for various items and statements, [Array types]
| `:`    | Colon       | Various separators
| `::`   | PathSep     | [Path separator][paths]
| `->`   | RArrow      | [Function return type][functions], [Closure return type][closures], [Function pointer type]
| `=>`   | FatArrow    | [Match arms][match], [Macros]
| `<-`   | LArrow      | The left arrow symbol has been unused since before Rust 1.0, but it is still treated as a single token
| `#`    | Pound       | [Attributes]
| `$`    | Dollar      | [Macros]
| `?`    | Question    | [Try propagation expressions][question], [Questionably sized][sized], [Macro Kleene Matcher][macros]
| `~`    | Tilde       | The tilde operator has been unused since before Rust 1.0, but its token may still be used

r[lex.token.delim]
## Delimiters

Bracket punctuation is used in various parts of the grammar. An open bracket
must always be paired with a close bracket. 
<!-- Brackets and the tokens within them are referred to as "token trees" in [macros].  -->
The three types of brackets are:

| Bracket | Type            |
|---------|-----------------|
| `{` `}` | Curly braces    |
| `[` `]` | Square brackets |
| `(` `)` | Parentheses     |


[Inferred types]: types/inferred.md
[Range patterns]: patterns.md#range-patterns
[Reference patterns]: patterns.md#reference-patterns
[Subpattern binding]: patterns.md#identifier-patterns
[Wildcard patterns]: patterns.md#wildcard-pattern
[arith]: expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[array types]: types/array.md
[assignment]: expressions/operator-expr.md#assignment-expressions
[attributes]: attributes.md
[borrow]: expressions/operator-expr.md#borrow-operators
[closures]: expressions/closure-expr.md
[comparison]: expressions/operator-expr.md#comparison-operators
[compound]: expressions/operator-expr.md#compound-assignment-expressions
[constants]: items/constant-items.md
[dereference]: expressions/operator-expr.md#the-dereference-operator
[destructuring assignment]: expressions/underscore-expr.md
[extern crates]: items/extern-crates.md
[extern]: items/external-blocks.md
[field]: expressions/field-expr.md
[Floating-point literal expressions]: expressions/literal-expr.md#floating-point-literal-expressions
[floating-point types]: types/numeric.md#floating-point-types
[function pointer type]: types/function-pointer.md
[functions]: items/functions.md
[generics]: items/generics.md
[identifier]: identifiers.md
[if let]: expressions/if-expr.md#if-let-patterns
[Integer literal expressions]: expressions/literal-expr.md#integer-literal-expressions
[keywords]: keywords.md
[lazy-bool]: expressions/operator-expr.md#lazy-boolean-operators
[literal expressions]: expressions/literal-expr.md
[macros]: macros-by-example.md
[match]: expressions/match-expr.md
[negation]: expressions/operator-expr.md#negation-operators
[negative impls]: items/implementations.md
[never type]: types/never.md
[numeric types]: types/numeric.md
[paths]: paths.md
[patterns]: patterns.md
[question]: expressions/operator-expr.md#the-try-propagation-expression
[range]: expressions/range-expr.md
[rangepat]: patterns.md#range-patterns
[raw pointers]: types/pointer.md#raw-pointers-const-and-mut
[references]: types/pointer.md
[sized]: trait-bounds.md#sized
[String continuation escapes]: expressions/literal-expr.md#string-continuation-escapes
[struct expressions]: expressions/struct-expr.md
[trait bounds]: trait-bounds.md
[tuple index]: expressions/tuple-expr.md#tuple-indexing-expressions
[tuple structs]: items/structs.md
[tuple variants]: items/enumerations.md
[tuples]: types/tuple.md
[unary minus operator]: expressions/operator-expr.md#negation-operators
[use declarations]: items/use-declarations.md
[use wildcards]: items/use-declarations.md
[while let]: expressions/loop-expr.md#while-let-patterns

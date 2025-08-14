r[expr.literal]
# Literal expressions

r[expr.literal.syntax]
```grammar,expressions
LiteralExpression ->
      CHAR_LITERAL
    | STRING_LITERAL
    | RAW_STRING_LITERAL
    | C_STRING_LITERAL
    | RAW_C_STRING_LITERAL
    | INTEGER_LITERAL
    | `true`
    | `false`
```

r[expr.literal.intro]
A _literal expression_ is an expression consisting of a single token, rather than a sequence of tokens, that immediately and directly denotes the value it evaluates to, rather than referring to it by name or some other evaluation rule.

r[expr.literal.const-expr]
A literal is a form of [constant expression], so is evaluated (primarily) at compile time.

r[expr.literal.literal-token]
Each of the lexical [literal][literal tokens] forms described earlier can make up a literal expression, as can the keywords `true` and `false`.

```rust
"hello";   // string type
'5';       // character type
5;         // integer type
```

r[expr.literal.string-representation]
In the descriptions below, the _string representation_ of a token is the sequence of characters from the input which matched the token's production in a *Lexer* grammar snippet.

> [!NOTE]
> This string representation never includes a character `U+000D` (CR) immediately followed by `U+000A` (LF): this pair would have been previously transformed into a single `U+000A` (LF).

r[expr.literal.escape]
## Escapes

r[expr.literal.escape.intro]
The descriptions of textual literal expressions below make use of several forms of _escape_.

r[expr.literal.escape.sequence]
Each form of escape is characterised by:
 * an _escape sequence_: a sequence of characters, which always begins with `U+005C` (`\`)
 * an _escaped value_: either a single character or an empty sequence of characters

In the definitions of escapes below:
 * An _octal digit_ is any of the characters in the range \[`0`-`7`].
 * A _hexadecimal digit_ is any of the characters in the ranges \[`0`-`9`], \[`a`-`f`], or \[`A`-`F`].

r[expr.literal.escape.simple]
### Simple escapes

Each sequence of characters occurring in the first column of the following table is an escape sequence.

In each case, the escaped value is the character given in the corresponding entry in the second column.

| Escape sequence | Escaped value            |
|-----------------|--------------------------|
| `\0`            | U+0000 (NUL)             |
| `\t`            | U+0009 (HT)              |
| `\n`            | U+000A (LF)              |
| `\r`            | U+000D (CR)              |
| `\"`            | U+0022 (QUOTATION MARK)  |
| `\'`            | U+0027 (APOSTROPHE)      |
| `\\`            | U+005C (REVERSE SOLIDUS) |

r[expr.literal.escape.hex-octet]
### 8-bit escapes

The escape sequence consists of `\x` followed by two hexadecimal digits.

The escaped value is the character whose [Unicode scalar value] is the result of interpreting the final two characters in the escape sequence as a hexadecimal integer, as if by [`u8::from_str_radix`] with radix 16.

> [!NOTE]
> The escaped value therefore has a [Unicode scalar value] in the range of [`u8`][numeric types].

r[expr.literal.escape.hex-ascii]
### 7-bit escapes

The escape sequence consists of `\x` followed by an octal digit then a hexadecimal digit.

The escaped value is the character whose [Unicode scalar value] is the result of interpreting the final two characters in the escape sequence as a hexadecimal integer, as if by [`u8::from_str_radix`] with radix 16.

r[expr.literal.escape.unicode]
### Unicode escapes

The escape sequence consists of `\u{`, followed by a sequence of characters each of which is a hexadecimal digit or `_`, followed by `}`.

The escaped value is the character whose [Unicode scalar value] is the result of interpreting the hexadecimal digits contained in the escape sequence as a hexadecimal integer, as if by [`u32::from_str_radix`] with radix 16.

> [!NOTE]
> The permitted forms of a [CHAR_LITERAL] or [STRING_LITERAL] token ensure that there is such a character.

r[expr.literal.continuation]
### String continuation escapes

The escape sequence consists of `\` followed immediately by `U+000A` (LF), and all following whitespace characters before the next non-whitespace character.
For this purpose, the whitespace characters are `U+0009` (HT), `U+000A` (LF), `U+000D` (CR), and `U+0020` (SPACE).

The escaped value is an empty sequence of characters.

> [!NOTE]
> The effect of this form of escape is that a string continuation skips following whitespace, including additional newlines. Thus `a`, `b` and `c` are equal:
>
> ```rust
> let a = "foobar";
> let b = "foo\
>          bar";
> let c = "foo\
>
>      bar";
>
> assert_eq!(a, b);
> assert_eq!(b, c);
> ```
>
> Skipping additional newlines (as in example c) is potentially confusing and unexpected. This behavior may be adjusted in the future. Until a decision is made, it is recommended to avoid relying on skipping multiple newlines with line continuations. See [this issue](https://github.com/rust-lang/reference/pull/1042) for more information.

r[expr.literal.char]
## Character literal expressions

r[expr.literal.char.intro]
A character literal expression consists of a single [CHAR_LITERAL] token.

r[expr.literal.char.type]
The expression's type is the primitive [`char`][textual types] type.

r[expr.literal.char.no-suffix]
The token must not have a suffix.

r[expr.literal.char.literal-content]
The token's _literal content_ is the sequence of characters following the first `U+0027` (`'`) and preceding the last `U+0027` (`'`) in the string representation of the token.

r[expr.literal.char.represented]
The literal expression's _represented character_ is derived from the literal content as follows:

r[expr.literal.char.escape]
* If the literal content is one of the following forms of escape sequence, the represented character is the escape sequence's escaped value:
    * [Simple escapes]
    * [7-bit escapes]
    * [Unicode escapes]

r[expr.literal.char.single]
* Otherwise the represented character is the single character that makes up the literal content.

r[expr.literal.char.result]
The expression's value is the [`char`][textual types] corresponding to the represented character's [Unicode scalar value].

> [!NOTE]
> The permitted forms of a [CHAR_LITERAL] token ensure that these rules always produce a single character.

Examples of character literal expressions:

```rust
'R';                               // R
'\'';                              // '
'\x52';                            // R
'\u{00E6}';                        // LATIN SMALL LETTER AE (U+00E6)
```

r[expr.literal.string]
## String literal expressions

r[expr.literal.string.intro]
A string literal expression consists of a single [STRING_LITERAL] or [RAW_STRING_LITERAL] token.

r[expr.literal.string.type]
The expression's type is a shared reference (with `static` lifetime) to the primitive [`str`][textual types] type.
That is, the type is `&'static str`.

r[expr.literal.string.no-suffix]
The token must not have a suffix.

r[expr.literal.string.literal-content]
The token's _literal content_ is the sequence of characters following the first `U+0022` (`"`) and preceding the last `U+0022` (`"`) in the string representation of the token.

r[expr.literal.string.represented]
The literal expression's _represented string_ is a sequence of characters derived from the literal content as follows:

r[expr.literal.string.escape]
* If the token is a [STRING_LITERAL], each escape sequence of any of the following forms occurring in the literal content is replaced by the escape sequence's escaped value.
    * [Simple escapes]
    * [7-bit escapes]
    * [Unicode escapes]
    * [String continuation escapes]

  These replacements take place in left-to-right order.
  For example, the token `"\\x41"` is converted to the characters `\` `x` `4` `1`.

r[expr.literal.string.raw]
* If the token is a [RAW_STRING_LITERAL], the represented string is identical to the literal content.

r[expr.literal.string.result]
The expression's value is a reference to a statically allocated [`str`][textual types] containing the UTF-8 encoding of the represented string.

Examples of string literal expressions:

```rust
"foo"; r"foo";                     // foo
"\"foo\""; r#""foo""#;             // "foo"

"foo #\"# bar";
r##"foo #"# bar"##;                // foo #"# bar

"\x52"; "R"; r"R";                 // R
"\\x52"; r"\x52";                  // \x52
```

r[expr.literal.c-string]
## C string literal expressions

r[expr.literal.c-string.intro]
A C string literal expression consists of a single [C_STRING_LITERAL] or [RAW_C_STRING_LITERAL] token.

r[expr.literal.c-string.type]
The expression's type is a shared reference (with `static` lifetime) to the standard library [CStr] type.
That is, the type is `&'static core::ffi::CStr`.

r[expr.literal.c-string.no-suffix]
The token must not have a suffix.

r[expr.literal.c-string.literal-content]
The token's _literal content_ is the sequence of characters following the first `"` and preceding the last `"` in the string representation of the token.

r[expr.literal.c-string.represented]
The literal expression's _represented bytes_ are a sequence of bytes derived from the literal content as follows:

r[expr.literal.c-string.escape]
* If the token is a [C_STRING_LITERAL], the literal content is treated as a sequence of items, each of which is either a single Unicode character other than `\` or an [escape].
The sequence of items is converted to a sequence of bytes as follows:
  * Each single Unicode character contributes its UTF-8 representation.
  * Each [simple escape] contributes the [Unicode scalar value] of its escaped value.
  * Each [8-bit escape] contributes a single byte containing the [Unicode scalar value] of its escaped value.
  * Each [unicode escape] contributes the UTF-8 representation of its escaped value.
  * Each [string continuation escape] contributes no bytes.

r[expr.literal.c-string.raw]
* If the token is a [RAW_C_STRING_LITERAL], the represented bytes are the UTF-8 encoding of the literal content.

> [!NOTE]
> The permitted forms of [C_STRING_LITERAL] and [RAW_C_STRING_LITERAL] tokens ensure that the represented bytes never include a null byte.

r[expr.literal.c-string.result]
The expression's value is a reference to a statically allocated [CStr] whose array of bytes contains the represented bytes followed by a null byte.

Examples of C string literal expressions:

```rust
c"foo"; cr"foo";                     // foo
c"\"foo\""; cr#""foo""#;             // "foo"

c"foo #\"# bar";
cr##"foo #"# bar"##;                 // foo #"# bar

c"\x52"; c"R"; cr"R";                // R
c"\\x52"; cr"\x52";                  // \x52

c"æ";                                // LATIN SMALL LETTER AE (U+00E6)
c"\u{00E6}";                         // LATIN SMALL LETTER AE (U+00E6)
c"\xC3\xA6";                         // LATIN SMALL LETTER AE (U+00E6)

c"\xE6".to_bytes();                  // [230]
c"\u{00E6}".to_bytes();              // [195, 166]
```

r[expr.literal.int]
## Integer literal expressions

r[expr.literal.int.intro]
An integer literal expression consists of a single [INTEGER_LITERAL] token.

r[expr.literal.int.suffix]
If the token has a [suffix], the suffix must be the name of one of the [primitive integer types][numeric types]: `u32`, `i32`, `usize`, or `isize`, and the expression has that type.

r[expr.literal.int.infer]
If the token has no suffix, the expression's type is determined by type inference:

r[expr.literal.int.inference-unique-type]
* If an integer type can be _uniquely_ determined from the surrounding program context, the expression has that type.

r[expr.literal.int.inference-default]
* If the program context under-constrains the type, it defaults to the signed 32-bit integer `i32`.

r[expr.literal.int.inference-error]
* If the program context over-constrains the type, it is considered a static type error.

Examples of integer literal expressions:

```rust
123;                               // type i32
123i32;                            // type i32
123u32;                            // type u32
123_u32;                           // type u32
let a: u64 = 123;                  // type u64

0xff;                              // type i32
0xff_u8;                           // type u8

0o70;                              // type i32
0o70_i16;                          // type i16

0b1111_1111_1001_0000;             // type i32
0b1111_1111_1001_0000i64;          // type i64

0usize;                            // type usize
```

r[expr.literal.int.representation]
The value of the expression is determined from the string representation of the token as follows:

r[expr.literal.int.radix]
* An integer radix is chosen by inspecting the first two characters of the string, as follows:

    * `0b` indicates radix 2
    * `0o` indicates radix 8
    * `0x` indicates radix 16
    * otherwise the radix is 10.

r[expr.literal.int.radix-prefix-stripped]
* If the radix is not 10, the first two characters are removed from the string.

r[expr.literal.int.type-suffix-stripped]
* Any suffix is removed from the string.

r[expr.literal.int.separators-stripped]
* Any underscores are removed from the string.

r[expr.literal.int.u128-value]
* The string is converted to a `u128` value as if by [`u128::from_str_radix`] with the chosen radix.
If the value does not fit in `u128`, it is a compiler error.

r[expr.literal.int.cast]
* The `u128` value is converted to the expression's type via a [numeric cast].

> [!NOTE]
> The final cast will truncate the value of the literal if it does not fit in the expression's type. `rustc` includes a [lint check] named `overflowing_literals`, defaulting to `deny`, which rejects expressions where this occurs.

> [!NOTE]
> `-1i8`, for example, is an application of the [negation operator] to the literal expression `1i8`, not a single integer literal expression. See [Overflow] for notes on representing the most negative value for a signed type.

r[expr.literal.bool]
## Boolean literal expressions

r[expr.literal.bool.intro]
A boolean literal expression consists of one of the keywords `true` or `false`.

r[expr.literal.bool.result]
The expression's type is the primitive [boolean type], and its value is:
 * true if the keyword is `true`
 * false if the keyword is `false`

[Escape]: #escapes
[Simple escape]: #simple-escapes
[Simple escapes]: #simple-escapes
[8-bit escape]: #8-bit-escapes
[8-bit escapes]: #8-bit-escapes
[7-bit escape]: #7-bit-escapes
[7-bit escapes]: #7-bit-escapes
[Unicode escape]: #unicode-escapes
[Unicode escapes]: #unicode-escapes
[String continuation escape]: #string-continuation-escapes
[String continuation escapes]: #string-continuation-escapes
[boolean type]: ../types/boolean.md
[constant expression]: ../const_eval.md#constant-expressions
[CStr]: core::ffi::CStr
[floating-point types]: ../types/numeric.md#floating-point-types
[lint check]: ../attributes/diagnostics.md#lint-check-attributes
[literal tokens]: ../tokens.md#literals
[numeric cast]: operator-expr.md#numeric-cast
[numeric types]: ../types/numeric.md
[suffix]: ../tokens.md#suffixes
[negation operator]: operator-expr.md#negation-operators
[overflow]: operator-expr.md#overflow
[textual types]: ../types/textual.md
[Unicode scalar value]: http://www.unicode.org/glossary/#unicode_scalar_value
[Unicode scalar values]: http://www.unicode.org/glossary/#unicode_scalar_value
[`f32::from_str`]: ../../core/primitive.f32.md#method.from_str
[`f64::from_str`]: ../../core/primitive.f64.md#method.from_str

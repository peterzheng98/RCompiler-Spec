r[comments]
# Comments

r[comments.syntax]
```grammar,lexer
@root LINE_COMMENT ->
      `//` ~LF*

BLOCK_COMMENT ->
      `/*` (~`*/`)* `*/`
```

r[comments.normal]
## Non-doc comments

Comments follow the general C++ style of line (`//`) and
block (`/* ... */`) comment forms. Nested block comments are supported.

r[comments.normal.tokenization]
Non-doc comments are interpreted as a form of whitespace.

r[comments.normal.examples]
### Non-doc comments examples

```rust
// This is a valid line comment

/* This is a valid block comment */

/* Nested block comments are supported:
   /* inner comment */
   More content here
*/

//   - Only a comment
//// - Also only a comment (multiple slashes beyond 3 are regular comments)

/*   - Only a comment */
/*** - Only a comment (multiple asterisks beyond 2 are regular comments) */

pub mod nested_comments {
    /* we can /* nest comments */ */
    
    // empty line comment
    //
    
    // empty block comment
    /**/
}
```

r[comments.doc]
## Doc comments (Undefined Behavior)

> [!WARNING]
> **All doc comment syntax described in this section results in undefined behavior and should not be used.**

r[comments.doc.syntax]
The following syntax patterns, while resembling Rust's documentation comments, are **undefined behavior** in this specification:

Line doc comments beginning with exactly _three_ slashes (`///`), and block
doc comments (`/** ... */`), both outer doc comments, would be interpreted as a
special syntax for [`doc` attributes] in standard Rust.

r[comments.doc.attributes]
In standard Rust, they would be equivalent to writing
`#[doc="..."]` around the body of the comment, i.e., `/// Foo` turns into
`#[doc="Foo"]` and `/** Bar */` turns into `#[doc="Bar"]`. However, **using these patterns results in undefined behavior**.

r[comments.doc.inner-syntax]
Line comments beginning with `//!` and block comments `/*! ... */` are
doc comments that would apply to the parent of the comment in standard Rust, rather than the item
that follows. **These patterns also result in undefined behavior**.

r[comments.doc.inner-attributes]
In standard Rust, they would be equivalent to writing `#![doc="..."]` around
the body of the comment. `//!` comments are usually used to document
modules that occupy a source file. **However, all such usage results in undefined behavior**.

r[comments.doc.bare-crs]
The character `U+000D` (CR) is not allowed in doc comments. **Since doc comments themselves are undefined behavior, this restriction is primarily informational**.

> [!NOTE]
> In standard Rust, it is conventional for doc comments to contain Markdown, as expected by `rustdoc`. However, the comment syntax does not respect any internal Markdown. ``/** `glob = "*/*.rs";` */`` terminates the comment at the first `*/`, and the remaining code would cause a syntax error. This slightly limits the content of block doc comments compared to line doc comments. **Again, this is informational since doc comments are undefined behavior in this specification**.

r[comments.doc.examples]
### Doc comments examples (All Undefined Behavior)

> [!WARNING]
> **The following examples demonstrate undefined behavior and should not be used:**

```rust
//! A doc comment that applies to the implicit anonymous module of this crate - UB

pub mod outer_module {

    //!  - Inner line doc - UB
    //!! - Still an inner line doc (but with a bang at the beginning) - UB

    /*!  - Inner block doc - UB */
    /*!! - Still an inner block doc (but with a bang at the beginning) - UB */

    ///  - Outer line doc (exactly 3 slashes) - UB

    /**  - Outer block doc (exactly) 2 asterisks - UB */

    pub mod inner_module {}

    pub mod degenerate_cases {
        // empty inner line doc - UB
        //!

        // empty inner block doc - UB
        /*!*/

        // empty outer line doc - UB
        ///

        pub mod dummy_item {}

    }

    /* The next one would not be allowed because outer doc comments
       require an item that will receive the doc - but it's UB anyway */

    /// Where is my item? - UB
#   mod boo {}
}
```

[`doc` attributes]: ../rustdoc/the-doc-attribute.html

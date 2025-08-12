r[comments]
# Comments

r[comments.syntax]
```grammar,lexer
@root LINE_COMMENT ->
      `//` ~LF*

BLOCK_COMMENT ->
      `/*` BLOCK_COMMENT_CONTENT* `*/`

BLOCK_COMMENT_CONTENT ->
      BLOCK_COMMENT
    | ~[`*` `/`]
    | `*` ~`/`
    | `/` ~`*`
```

r[comments.normal]
## (Non-doc) comments

Comments follow the general C++ style of line (`//`) and
block (`/* ... */`) comment forms. Nested block comments are supported.

r[comments.normal.tokenization]
(Non-doc) comments are interpreted as a form of whitespace.

r[comments.normal.examples]
### (Non-doc) comments examples

```rust
// This is a valid line comment

/* This is a valid block comment */

/* Nested block comments are supported:
   /* inner comment */
   More content here
*/

//   - A comment  
//// - Also a comment

/*   - A comment */
/*** - A comment */

pub mod nested_comments {
    /* we can /* nest /* deeply */ nested */ comments */
    
    // empty line comment
    //
    
    // empty block comment
    /**/
}
```

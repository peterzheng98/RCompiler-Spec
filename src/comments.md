r[comments]
# Comments

r[comments.syntax]
```grammar,lexer
@root LINE_COMMENT ->
      `//` ~LF*

@root BLOCK_COMMENT ->
      `/*` BLOCK_COMMENT_CONTENT* `*/`

BLOCK_COMMENT_CONTENT ->
      BLOCK_COMMENT
    | ~[`*` `/`]
    | `*` ~`/`
    | `/` ~`*`
```

Comments follow the general C++ style of line (`//`) and
block (`/* ... */`) comment forms. Nested block comments are supported.

r[comments.normal.tokenization]
Comments are interpreted as a form of whitespace.

r[comments.normal.examples]
### Examples

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

/* we can /* nest /* deeply */ nested */ comments */

// empty line comment
//

// empty block comment
/**/

/* /* You should strictly match /* and */ of the nested block comment. */
fn main(){} // this is a comment not a code piece
*/
```

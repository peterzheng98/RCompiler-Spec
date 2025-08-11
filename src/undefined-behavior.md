r[ub]
# Undefined behavior
Undefined behavior is a term used to describe the result of executing code that does not conform to the rules of the language specification. **In this project, undefined behavior means code with undefined behavior will not be in the test suite. If there is a test that has undefined behavior, any output is considered valid, and the test will not fail.**

**If any section contains undefined behavior mentioned in this section, this chapter shall prevail.**


r[ub.overall]
## Undefined behavior list (Overall)

r[ub.overall.source-size]
### Source code size limit

Source code files exceeding 1M bytes in size are considered undefined behavior.

r[ub.overall.non-ascii]
### Non-ASCII characters

Source code containing any extended ASCII characters or multi-byte Unicode characters (all characters with code points greater than 127) is considered undefined behavior. Only 7-bit ASCII characters (code points 0-127) are allowed.

r[ub.overall.panic]
### Panic

Code that panics is considered undefined behavior. This includes using the `panic!` macro,
calling functions that panic, or any other situation that causes the program to panic.

r[ub.overall.uninitialized]

### Uninitialized variables

Using uninitialized variables is considered undefined behavior. This includes reading from a variable that has not been initialized, or using a variable that has been initialized but not assigned a value.

r[ub.overall.ownership]

### Ownership violations

To simplify the language, ownership violations are considered undefined behavior. This includes using a value after it has been moved, or using a value that has been dropped.

You can simply think that your compiler do not need to handle ownership violations, and it is not required to do so.

r[ub.overall.macros]
### Syntactic macros

All syntactic macros are considered undefined behavior. This includes:
- Declarative macros (`macro_rules!`)
- Procedural macros
- Attribute macros
- Function-like macros
- Any macro invocation or definition

r[ub.overall.unsafe]
### Unsafe operations

All unsafe operations are considered undefined behavior. 

r[ub.overall.lifetimes]
### Lifetime-related code

All code that would cause lifetime errors and all lifetime-related syntax are considered undefined behavior.

And the compiler is not required to perform any lifetime analysis or checking.

r[ub.overall.genericity]
### Genericity

All code that relies on genericity is considered undefined behavior except `Box<T>`, `Option<T>` and `Result<T>`.

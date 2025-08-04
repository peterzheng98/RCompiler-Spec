r[undefined-behavior]
# Undefined behavior
Undefined behavior is a term used to describe the result of executing code that does not conform to the rules of the language specification. In this project, undefined behavior means code with undefined behavior will not be in the test suite. If there is a test that has undefined behavior, any output is considered valid, and the test will not fail.

If any section contains undefined behavior mentioned in this section, this chapter shall prevail.


## Undefined behavior list.
r[undefined-behavior.panic]
### Panic
Code that panics is considered undefined behavior. This includes using the `panic!` macro,
calling functions that panic, or any other situation that causes the program to panic.
r[undefined-behavior.uninitialized]
### Uninitialized variables
Using uninitialized variables is considered undefined behavior. This includes reading from a variable that has not been initialized, or using a variable that has been initialized but not assigned a value.
r[undefined-behavior.ownership]
### Ownership violations
To simplify the language, ownership violations are considered undefined behavior. This includes using a value after it has been moved, or using a value that has been dropped.

You can simply think that your compiler do not need to handle ownership violations, and it is not required to do so.
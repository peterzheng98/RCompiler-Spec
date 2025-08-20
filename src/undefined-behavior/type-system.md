r[ub.type-system]
# Undefined behavior list (Type System)

r[ub.type-system.undefined-types]
## Undefined types

The following types never occur in the testcases, so neither these types themselves nor any related features of them are required to be implemented.

- Function item and pointer types;
- Closure types;

r[ub.type-system.numeric-types]
## Numeric types

- Any numeric types except `u32`, `i32`, `usize` and `isize` are not defined (remember your target machine is 32-bit).
- Suffix is still required, e.g., `114514_i32`, `19260817u32`.

r[ub.type-system.inferred-type]
## Inferred type

Inferred type is not required. 
<!-- It is promised that all the inferred types in the test suite can be determined uniquely during compilation. -->

Furthermore, there is no place where type inference is needed.

r[ub.type-system.unit-type]
## Unit type

Any expressions, items and patterns about tuple have been declared as undefined behaviors, but unit type `()` is still required.

r[ub.type-system.slice-type]
## Slice types

Slice types are not defined.

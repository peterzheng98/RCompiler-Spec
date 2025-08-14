r[type.pointer]
# Pointer types

r[type.pointer.intro]
All pointers are explicit first-class values.
They can be moved or copied, stored into data structs, and returned from functions.

r[type.pointer.reference]
## References (`&` and `&mut`)

r[type.pointer.reference.syntax]
```grammar,types
ReferenceType -> `&` `mut`? TypeNoBounds
```

r[type.pointer.reference.shared]
### Shared references (`&`)

r[type.pointer.reference.shared.intro]
Shared references point to memory which is owned by some other value.

r[type.pointer.reference.shared.constraint-mutation]
When a shared reference to a value is created, it prevents direct mutation of the value.
[Interior mutability] provides an exception for this in certain circumstances.
As the name suggests, any number of shared references to a value may exist.
A shared reference type is written `&type`.

r[type.pointer.reference.shared.copy]
Copying a reference is a "shallow" operation:
it involves only copying the pointer itself, that is, pointers are `Copy`.
Releasing a reference has no effect on the value it points to, but referencing of a [temporary value] will keep it alive during the scope of the reference itself.

r[type.pointer.reference.mut]
### Mutable references (`&mut`)

r[type.pointer.reference.mut.intro]
Mutable references point to memory which is owned by some other value.
A mutable reference type is written `&mut type`.

r[type.pointer.reference.mut.copy]
A mutable reference (that hasn't been borrowed) is the only way to access the value it points to, so is not `Copy`.

r[type.pointer.validity]
## Bit validity

r[type.pointer.validity.pointer-fragment]
Despite pointers and references being similar to `usize`s in the machine code emitted on most platforms,
the semantics of transmuting a reference or pointer type to a non-pointer type is currently undecided.
Thus, it may not be valid to transmute a pointer or reference type, `P`, to a `[u8; size_of::<P>()]`.

[Interior mutability]: ../interior-mutability.md
[`unsafe` operation]: ../unsafety.md
[dynamically sized types]: ../dynamically-sized-types.md
[temporary value]: ../expressions.md#temporaries

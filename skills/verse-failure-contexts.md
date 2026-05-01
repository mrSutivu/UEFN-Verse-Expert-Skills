---
name: verse-failure-contexts
description: Understanding Verse's unique control flow using <decides>, failure contexts, and automatic state rollback.
---

# Verse Failure Contexts (UEFN Expert)

## Activation / When to Use
- Mandatory when writing any conditional logic, accessing array elements, or validating state in Verse.
- Trigger when avoiding exceptions or boolean returns for validation.

## 1. The Core Principle: Failure vs Success
Verse does NOT use `throw`, `catch`, or `return false` for errors. 
Expressions either **succeed** (produce a value) or **fail** (terminate the current execution path). Failure is a valid way to control program flow.

## 2. The `<decides>` Specifier
If a function can fail, it MUST be marked with `<decides>`. It usually also needs `<transacts>` if it modifies variables (so the engine can roll back changes).

```verse
# This function fails if N is less than or equal to 0
IsPositive(N:int)<decides>:void=
    N > 0 
```

## 3. Failure Contexts
A failable expression (`<decides>`) CANNOT be called just anywhere. It must be inside a "Failure Context" that knows how to catch the failure.

### The `if` Context
The most common. If the expression fails, the `else` block runs.
```verse
if (IsPositive[MyNumber]):
    Print("It's positive!")
else:
    Print("It failed (negative or zero).")
```

### The `for` Context (Filtering)
If a failable expression is inside a `for` loop definition, failure simply skips that iteration. It acts as a powerful filter.
```verse
# Only prints numbers > 0. Failures are silently skipped.
for (Num : NumberArray, IsPositive[Num]):
    Print("Positive: {Num}")
```

## 4. The Rollback Feature (<transacts>)
If a block of code modifies a `var` (variable), and then fails, Verse **automatically rolls back** all variable modifications made in that block.
*Note: Only variables marked with `<transacts>` (or inside `<transacts>` functions) are rolled back.*

## 5. Impact on Safety
- **No Null Pointer Exceptions**: Array bounds checking (`MyArray[Index]`) is a failable expression. If the index is out of bounds, it fails gracefully instead of crashing the server.
- **State Integrity**: Automatic rollbacks prevent partial state updates (e.g., deducting gold from a player but failing to give the item).

## Verification Checklist
- [ ] Functions that can fail are marked with `<decides>`.
- [ ] Array access `[]` is always wrapped in a failure context like `if`.
- [ ] Boolean logic relies on success/failure rather than returning `true/false`.
- [ ] Variables modified in failable contexts are understood to be rolled back on failure.

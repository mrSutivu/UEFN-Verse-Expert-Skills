---
name: verse-option-types
description: Handling Optional values (?type) in Verse to represent absence without Null Pointer Exceptions.
---

# Verse Option Types (UEFN Expert)

## Activation / When to Use
- Mandatory when returning a value that might not exist (e.g., finding a player, looking up a map key).
- Trigger when dealing with `false` or "empty" states in Verse.

## 1. The Core Principle
Verse DOES NOT have `null`. Instead, it uses Option Types (`?type`).
A variable of type `?int` can either hold an integer (e.g., `5`) or hold the special value `false` (meaning empty/absent).

## 2. Declaration Syntax
```verse
# Option containing an int
var MyOption : ?int = false

# Assigning a value
set MyOption = option{42}
```

## 3. Unwrapping (Failable Context)
You CANNOT use an Option type directly in math or logic. You must "unwrap" it to extract the real value. Unwrapping is a failable expression (`<decides>`), meaning it must be done in an `if` or `for`.

```verse
# BAD: Compiler error, cannot add float to ?float
# Result := MyOption + 5.0 

# GOOD: Unwrap via query 'Option?'
if (ActualValue := MyOption?):
    Result := ActualValue + 5.0
else:
    Print("MyOption was empty (false)")
```

## 4. Map Lookups return Options
When you access a `[key]` in a map, Verse returns an Option, because the key might not exist.

```verse
MyMap : [string]int = map{"Apple" => 10, "Banana" => 5}

# Map access returns ?int
if (AppleCount := MyMap["Apple"]):
    Print("Apples: {AppleCount}")
```

## 5. Impact on Safety
- **Zero Null Ref Crashes**: Because the compiler forces you to use an `if` block (failable context) to extract the value via `?`, it is mathematically impossible to experience a Null Pointer Exception at runtime.

## Verification Checklist
- [ ] Variables that can be empty use the `?` prefix (e.g., `?agent`).
- [ ] Options are initialized to `false` when empty.
- [ ] Options are assigned values using `option{Value}`.
- [ ] Unwrapping is done strictly using the `Option?` query inside a failure context.

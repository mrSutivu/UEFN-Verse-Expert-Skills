---
name: verse-math-logic
description: Advanced math and logic conversions in Verse. Handling failable math (<decides>) like Mod, Ceil, Floor, and Int/Float casting.
---

# Math & Logic Conversions (UEFN Expert)

## Activation / When to Use
- Mandatory when performing mathematical operations, especially converting between `float` and `int`.
- Trigger when dealing with division, modulus, or rounding.

## 1. Type Conversions (Failable)
In Verse, converting a `float` to an `int` directly is **not natively supported via a simple cast** because it inherently loses data. You must use mathematical functions.

**CRITICAL**: Most conversion functions in Verse are FAILABLE (`<decides>`).

### Float to Int
Use `Floor`, `Ceil`, or `Round`. These return `float`. Then cast to `int` using the failable `Int[]` constructor.

```verse
using { /UnrealEngine.com/Temporary/SpatialMath } # Contains math functions

ConvertMath():void=
    MyFloat : float = 5.75
    
    # 1. Round/Floor/Ceil the float
    FlooredFloat := Floor(MyFloat) # Returns 5.0
    
    # 2. Cast to Int (FAILABLE)
    if (MyInt := Int[FlooredFloat]):
        Print("Integer is {MyInt}")
```

### Int to Float
Converting up is safe and does not fail. Simply multiply by `1.0`.

```verse
MyInt : int = 10
MyFloat : float = MyInt * 1.0
```

## 2. Mathematical Operations

### Modulus (Remainder)
Modulus `Mod` is failable because dividing by 0 is impossible.

```verse
CheckEven(Num : int):void=
    # Failable context required
    if (Mod[Num, 2] = 0):
        Print("Number is even")
```

### Absolute Value
`Abs` is safe for floats and ints.
```verse
PositiveValue := Abs(-50.0)
```

## 3. Impact on Safety
- **Divide by Zero**: Verse protects against division by zero by making operations like `Mod` failable. If the denominator is 0, the expression simply fails and hits the `else` block, rather than crashing the game.
- **Precision Loss**: Forcing `Int[]` to be failable ensures the programmer acknowledges the data loss when stripping decimals.

## Verification Checklist
- [ ] Conversions from `float` to `int` use `Floor/Ceil/Round` followed by the failable `Int[]`.
- [ ] `Int[]` is strictly executed inside an `if` or `for` block.
- [ ] `Mod[]` is executed inside a failable context.
- [ ] Int to Float uses `* 1.0` (or `Float()` if available in the specific API version).

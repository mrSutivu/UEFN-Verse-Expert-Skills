# Math & Logic Reference

## 1. Clamping Values
Restricting a value between a minimum and maximum.

```verse
using { /UnrealEngine.com/Temporary/SpatialMath }

ApplyDamage(Damage : float):void=
    var Health : float = 100.0
    
    # Subtract
    set Health -= Damage
    
    # Clamp between 0 and 100
    set Health = Clamp(Health, 0.0, 100.0)
```

## 2. Power and Exponents
```verse
CalculateArea(Radius : float):float=
    # Pow(Base, Exponent)
    return 3.14159 * Pow(Radius, 2.0)
```

## 3. Linear Interpolation (Lerp)
Smoothly transitioning between two values over a percentage (Alpha 0.0 to 1.0).

```verse
GetSmoothValue(Start : float, End : float, Alpha : float):float=
    return Lerp(Start, End, Alpha)
```

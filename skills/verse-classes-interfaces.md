---
name: verse-classes-interfaces
description: Defining object-oriented structures in Verse. Covering classes, interfaces, accessibility (<public>, <internal>), and <concrete>.
---

# Classes & Interfaces (UEFN Expert)

## Activation / When to Use
- Mandatory when organizing data and methods beyond standard `creative_device` scripts.
- Trigger when building reusable systems, managers, or defining Data Models (like WeaponStats).

## 1. Defining Classes
Classes in Verse define state (variables) and behavior (functions).

```verse
# A standard class
weapon_stats := class:
    # Variables must be initialized
    Damage : float = 10.0
    FireRate : float = 1.0

    GetDPS():float=
        return Damage * FireRate
```

### Instantiation
```verse
# Create an instance overriding defaults
MyRifle := weapon_stats{ Damage := 30.0, FireRate := 5.0 }
```

## 2. Defining Interfaces
Interfaces define a contract (methods) that a class must implement.
- **Rule**: Interface names should reflect behavior.

```verse
damageable := interface:
    TakeDamage(Amount : float):void

# Implementation
destructible_prop := class(damageable):
    var Health : float = 100.0

    # MUST use <override> to satisfy the interface
    TakeDamage<override>(Amount : float):void=
        set Health = Health - Amount
```

## 3. Accessibility Specifiers
Verse uses strict visibility to prevent unauthorized data mutation.
- `<public>`: Accessible from ANY module.
- `<internal>`: Accessible ONLY within the current module (Default).
- `<private>`: Accessible ONLY within the class itself.

```verse
player_data := class:
    # Publicly readable, privately writable
    var Score<public><private> : int = 0

    # Public function
    AddScore<public>(Amount : int):void=
        set Score = Score + Amount
```

## 4. The `<concrete>` Specifier
If you want to expose a custom class as an `@editable` property in UEFN so a designer can tweak its variables, the class MUST be marked `<concrete>`.

```verse
# Mark as concrete to allow Editor serialization
weapon_config := class<concrete>:
    @editable Damage : float = 10.0

my_device := class(creative_device):
    # Now exposed in the UEFN Details Panel
    @editable
    Config : weapon_config = weapon_config{}
```

## 5. Impact on Safety
- **Encapsulation**: Using `<public><private>` prevents a random device from resetting a player's score to 0 without going through the `AddScore()` validation logic.
- **Editor Crashes**: Forgetting `<concrete>` on a class you try to expose via `@editable` will cause immediate compilation errors in Verse.

## Verification Checklist
- [ ] Methods fulfilling an interface are marked with `<override>`.
- [ ] Variables modified internally but read externally use `<public><private>`.
- [ ] Custom classes exposed to the UEFN Editor via `@editable` are marked `<concrete>`.
- [ ] Variables inside classes are initialized with default values.

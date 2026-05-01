---
name: verse-player-hierarchy
description: Navigating the agent, player, and fort_character hierarchy. Safely casting to fetch Health, Shields, and apply Damage.
---

# Verse Player Hierarchy (UEFN Expert)

## Activation / When to Use
- Mandatory when you need to read or modify player stats (Health, Shield).
- Trigger when passing actors between devices (which use `agent`) and gameplay logic (which needs `fort_character`).

## 1. The Three Layers
In Fortnite/Verse, a character is split into three concepts:
- **`agent`**: The most generic base type. Represents any entity capable of action (Human, AI Guard, Animal). Devices output `agent`.
- **`player`**: A specific type of `agent` that is controlled by a human sitting at a screen.
- **`fort_character`**: The physical avatar in the world. It has a location, health, and a mesh.

## 2. Casting Down the Hierarchy
You almost always receive an `agent` from a device event. You must cast it to access physical properties.

### Agent -> FortCharacter
Use `GetFortCharacter[]`. This is a FAILABLE expression because the agent might be dead, unspawned, or disconnected.

```verse
using { /Fortnite.com/Characters }

OnButtonPushed(InAgent : agent):void=
    # Failable cast
    if (FortChar := InAgent.GetFortCharacter[]):
        # We now have access to physical properties
        Print("Got physical character!")
```

### Agent -> Player
Use if you need to access UI or specific human-only data. Also failable.

```verse
if (HumanPlayer := player[InAgent]):
    # Now you can fetch UI via GetPlayerUI[HumanPlayer]
```

## 3. Health & Shields
Once you have the `fort_character`, you can query stats. These return `float`.

```verse
if (FortChar := InAgent.GetFortCharacter[]):
    Health := FortChar.GetHealth()
    MaxHealth := FortChar.GetMaxHealth()
    Shield := FortChar.GetShield()
    
    # Modifying stats
    FortChar.SetHealth(50.0)
    FortChar.SetShield(100.0)
```

## 4. Impact on Safety
- **Healing vs Damage**: `SetHealth()` is strictly for setting a value. It CANNOT kill a player. If you `SetHealth(0.0)`, it clamps to `1.0`. To actually eliminate a player, you must use the `Damage()` function.
- **Null Safety**: The mandatory `if (FortChar := ...)` block guarantees the server will never crash trying to read the health of an agent that just disconnected.

## Verification Checklist
- [ ] `using { /Fortnite.com/Characters }` is included.
- [ ] `GetFortCharacter[]` is called inside a failable context (`if`).
- [ ] Stats (Health/Shield) are treated as `float`, not `int`.
- [ ] `Damage()` is used instead of `SetHealth()` if the goal is elimination.

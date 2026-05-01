---
name: verse-random-rng
description: Generating random numbers and shuffling arrays using the /Verse.org/Random module. Ensuring safe index bounds.
---

# Random Generation & RNG (UEFN Expert)

## Activation / When to Use
- Mandatory when spawning loot, picking a random player, choosing a random spawn point, or applying critical hit chances.
- Trigger when you need non-deterministic behavior in a match.

## 1. The `/Verse.org/Random` Module
All RNG functions are housed in this specific module. It must be imported.

```verse
using { /Verse.org/Random }
```

## 2. Generating Numbers

### `GetRandomInt(Low: int, High: int)`
Generates an integer between `Low` and `High` (inclusive).
- **Use Case**: Array indices, loot tables, dice rolls.

```verse
# Rolls a d20 (1 to 20)
Roll := GetRandomInt(1, 20)

if (Roll >= 15):
    Print("Critical Hit!")
```

### `GetRandomFloat(Low: float, High: float)`
Generates a floating-point number between `Low` and `High` (inclusive).
- **Use Case**: Timers, exact damage variance, speed multipliers.

```verse
# Random wait between 2.5 and 5.0 seconds
WaitTime := GetRandomFloat(2.5, 5.0)
Sleep(WaitTime)
```

## 3. Shuffling Arrays (`Shuffle`)
`Shuffle` takes an array and returns a **NEW** array with the elements in a random order. It does not modify the original array in place (Verse arrays are immutable).

```verse
var MySpawns : []player_spawner_device = array{Spawn1, Spawn2, Spawn3}

# Create a randomized copy
RandomizedSpawns := Shuffle(MySpawns)
```

## 4. Impact on Safety
- **Array Bounds (CRITICAL)**: When picking a random element from an array, you MUST use `GetRandomInt(0, Array.Length - 1)`. If you forget the `- 1`, you will eventually generate an index out of bounds, which fails the context.

**BAD (Out of Bounds Risk)**:
```verse
Index := GetRandomInt(0, MyArray.Length) # FATAL: Can generate MyArray.Length, which is out of bounds!
```

**GOOD (Safe Bounds)**:
```verse
if (MyArray.Length > 0):
    Index := GetRandomInt(0, MyArray.Length - 1)
    if (ChosenItem := MyArray[Index]):
        Print("Got random item!")
```

## Verification Checklist
- [ ] `using { /Verse.org/Random }` is imported.
- [ ] `GetRandomInt` uses `Array.Length - 1` when determining array indices.
- [ ] Arrays are checked for `Length > 0` before picking a random index.
- [ ] `Shuffle` results are assigned to a new variable or overwrite a `var` array.

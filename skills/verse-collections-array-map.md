---
name: verse-collections-array-map
description: Syntax and best practices for creating, accessing, and modifying Arrays and Maps in Verse.
---

# Verse Collections (UEFN Expert)

## Activation / When to Use
- Mandatory when managing lists of players, spawned items, or key-value data in Verse.
- Trigger when you need to group data logically.

## 1. Arrays (`[]type`)
Arrays in Verse are **immutable** by default if they are constant. To change an array, the variable must be marked `var`, and you replace the entire array.

### Initialization
```verse
# Empty array of ints
var MyInts : []int = array{}

# Pre-filled array
var PlayersToSpawn : []agent = array{Player1, Player2}
```

### Modification
You don't "push" to an array. You concatenate and reassign using `+`.
```verse
# Add an element
set MyInts = MyInts + array{42}

# Concatenate arrays
set MyInts = MyInts + array{10, 20}
```

### Accessing (Failable Context)
Accessing an index is a failable expression (`<decides>`). It MUST be checked.
```verse
if (FirstValue := MyInts[0]):
    Print("Got it!")
```

## 2. Maps (`[key]type`)
Maps associate a unique Key with a Value. 

### Initialization
```verse
# Map linking a String to an Int
var Inventory : [string]int = map{}

# Pre-filled
var WeaponDamage : [string]float = map{"Pistol" => 15.0, "Rifle" => 35.0}
```

### Modification
To add or change a value, you use `set` with the key accessor.
*Note: Setting a map value is also a failable expression (it can fail if the map is constant, though on a `var` it succeeds by adding the key).*
Actually, in Verse, you reassign the map or use the `set` syntax within a failable context.
```verse
if (set WeaponDamage["Shotgun"] = 50.0):
    Print("Added shotgun damage.")
```
*Correction*: To add/update without failing, you often use concatenation for maps:
```verse
set WeaponDamage = ConcatenateMaps(WeaponDamage, map{"Shotgun" => 50.0})
```
*Built-in approach*: `set WeaponDamage["Shotgun"] = 50.0` is failable because `WeaponDamage` must be a `var` and map assignment is strictly checked.

### Accessing (Option Return)
Reading a map returns an `Option` type (`?type`), because the key might not exist.
```verse
if (Damage := WeaponDamage["Rifle"]):
    Print("Rifle does {Damage}")
```

## 3. Impact on Safety
- **Immutability by Default**: Because arrays are often replaced entirely rather than mutated internally, race conditions in concurrent `race` blocks are heavily minimized.
- **Bounds Safety**: The strict enforcement of failable contexts (`if`) around `Array[Index]` guarantees your UEFN island will never crash due to an "Index Out Of Bounds" error.

## Verification Checklist
- [ ] Collections that need modification are declared with `var`.
- [ ] Arrays are appended using the `+ array{}` syntax.
- [ ] Map reads are unwrapped using `if` because they return Options.
- [ ] Array index reads are wrapped in an `if` because they are failable.

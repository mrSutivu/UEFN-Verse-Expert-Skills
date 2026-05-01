---
name: verse-spawning-despawning
description: Techniques for spawning items, AI, or props in the game world. Using Spawner Devices as proxies for instantiation.
---

# Spawning & Despawning (UEFN Expert)

## Activation / When to Use
- Mandatory when introducing new elements (Weapons, Enemies, Vehicles) into the world during gameplay.
- Trigger when you need to clean up actors to free memory.

## 1. The Verse Spawning Limitation
**CRITICAL**: Unlike Unreal C++ where you can call `GetWorld()->SpawnActor()`, Verse currently has limited native support for instantiating physical assets directly by class name.
- **The Solution**: You must use **Spawner Devices** placed in the UEFN level as proxies.

## 2. Using Item Spawners
To spawn a specific weapon or item, use the `item_spawner_device`.

```verse
using { /Fortnite.com/Devices }

my_device := class(creative_device):

    @editable
    WeaponSpawner : item_spawner_device = item_spawner_device{}

    GiveWeaponToPlayer(Agent : agent):void=
        # Triggers the device to spawn the item at its location or directly to the player
        WeaponSpawner.SpawnItem()
```

## 3. Spawning AI / Guards
Use the `npc_spawner_device` or `guard_spawner_device`.

```verse
    @editable
    GuardSpawner : guard_spawner_device = guard_spawner_device{}

    TriggerAmbush():void=
        GuardSpawner.Spawn()
```

## 4. Spawning Custom Props (SpawnProp)
For standard 3D meshes (Props), Verse *does* provide a native `SpawnProp` function, provided you expose the asset reference.

### Setting up the Asset
```verse
using { /Verse.org/Simulation }
using { /UnrealEngine.com/Temporary/SpatialMath }

    # Exposes an asset picker to the UEFN Editor
    @editable
    MyPropAsset : creative_prop_asset = DefaultCreativePropAsset
```

### Spawning
`SpawnProp` returns a tuple containing the SpawnResult and the Prop itself (as an `Option`).
```verse
    SpawnCustomProp(Location : vector3)<suspends>:void=
        # Call SpawnProp (returns tuple)
        SpawnResult := SpawnProp(MyPropAsset, Location, IdentityRotation())
        
        # Extract the prop from the tuple's option
        if (SpawnedProp := SpawnResult(1)?):
            Print("Prop Spawned Successfully!")
            # You can now call MoveTo or TeleportTo on SpawnedProp
```

## 5. Despawning (Disposing)
To remove a spawned prop from the world and free its memory, call `Dispose()`.

```verse
    if (PropToDestroy := MyOptionProp?):
        PropToDestroy.Dispose()
```

## Verification Checklist
- [ ] Spawner Devices are used for Items, Vehicles, and AI.
- [ ] `SpawnProp` is used exclusively for `creative_prop_asset`s.
- [ ] The return tuple of `SpawnProp` is properly unwrapped `Result(1)?` to retrieve the actual prop instance.
- [ ] `Dispose()` is called to clean up spawned props when no longer needed.

---
name: verse-vfx-spawner
description: Spawning and controlling Niagara particle effects dynamically using the vfx_spawner_device.
---

# VFX Spawner Device (UEFN Expert)

## Activation / When to Use
- Mandatory when triggering visual effects (Explosions, Magic Trails, Confetti) via Verse code.
- Trigger when you need to enable/disable an ambient effect based on gameplay logic.

## 1. The `vfx_spawner_device`
Unlike pure Unreal C++ where you use `SpawnSystemAtLocation`, Verse currently requires a physical proxy device in the level to play Niagara effects securely.

### Editor Setup
1. Place a `VFX Spawner` device.
2. Select your custom Niagara System in the Visual Effect setting.
3. Configure settings: "Spawn on Timer" (Off), "Enable on Phase" (None, if controlled via code).

## 2. Implementation Syntax

### Enabling and Disabling (Continuous Loops)
If your Niagara system is a continuous loop (like a campfire or a magic aura), use `Enable()` and `Disable()`.

```verse
using { /Fortnite.com/Devices }

my_vfx_manager := class(creative_device):

    @editable
    MagicAuraVFX : vfx_spawner_device = vfx_spawner_device{}

    ActivateAura():void=
        MagicAuraVFX.Enable()

    DeactivateAura():void=
        MagicAuraVFX.Disable()
```

### Begin and End (Burst Effects)
If your Niagara system is a one-shot burst (like an explosion), `Enable()` might not restart the particle system if it was already enabled. Use `Begin()` to force the particle system to fire from the start.

```verse
    PlayExplosion():void=
        # Fires the particle effect once
        ExplosionVFX.Begin()
```

## 3. Dynamic Relocation
Because you often have one VFX Spawner but want the explosion to happen at different locations (e.g., where a Raycast hit), you must teleport the proxy prop that holds the Spawner.

```verse
using { /UnrealEngine.com/Temporary/SpatialMath }

    @editable
    ExplosionProp : creative_prop = creative_prop{} # Prop holding the VFX Spawner

    PlayExplosionAt(TargetLoc : vector3):void=
        # 1. Teleport the prop instantly
        if (ExplosionProp.TeleportTo[TargetLoc, IdentityRotation()]):
            # 2. Trigger the VFX
            ExplosionVFX.Begin()
```

## 4. Impact on Safety
- **VFX Overlap Limit**: Calling `Begin()` 500 times a second on a single VFX Spawner will likely cause visual culling or performance hits. If you need 50 concurrent explosions, you need an array of 50 VFX Spawners (Object Pooling).
- **Prop Hierarchy**: The `vfx_spawner_device` itself cannot be easily `TeleportTo`'d. It MUST be attached as a child to a `creative_prop` in the Editor, and you move the `creative_prop` in Verse.

## Verification Checklist
- [ ] `vfx_spawner_device` is used as the interface to Niagara.
- [ ] `Begin()` is used for one-shot burst effects.
- [ ] `Enable()` / `Disable()` are used for toggling continuous looping effects.
- [ ] Dynamic locations are achieved by teleporting a parent `creative_prop`.

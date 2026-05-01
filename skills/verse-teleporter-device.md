---
name: verse-teleporter-device
description: Managing player movement natively using teleporter_device vs physical TeleportTo.
---

# Teleporter Device (UEFN Expert)

## Activation / When to Use
- Mandatory when moving a player across the map instantly with visual VFX/SFX.
- Trigger when implementing rifts, portals, or respawn mechanics.

## 1. TeleportTo vs Teleporter Device
- **`FortCharacter.TeleportTo[]`**: Raw mathematical teleport. Silent, instant. Good for background logic.
- **`teleporter_device`**: Fortnite's native Rift device. Includes sound effects, visual flashes, and maintains momentum (or resets it) based on settings. Good for player-facing mechanics.

## 2. Implementation Syntax

### Using the Device
```verse
using { /Fortnite.com/Devices }

my_portal_manager := class(creative_device):

    @editable RiftA : teleporter_device = teleporter_device{}
    @editable RiftB : teleporter_device = teleporter_device{}

    SendPlayerToB(Agent : agent):void=
        # Teleports the agent to the location of RiftB
        RiftB.Teleport(Agent)
```

### Listening to Teleports
You can execute logic when a player uses a portal.

```verse
    OnBegin<override>()<suspends>:void=
        RiftA.TeleportedEvent.Subscribe(OnPlayerUsedRift)

    OnPlayerUsedRift(Agent : agent):void=
        Print("Player entered Rift A!")
```

## 3. The Raw Math Alternative (`TeleportTo`)
If you do not want the visual "Rift" effect, you must use the `fort_character`'s native method.

```verse
    SilentTeleport(Agent : agent, Dest : vector3, Rot : rotation):void=
        if (FortChar := Agent.GetFortCharacter[]):
            # Failable expression
            if (FortChar.TeleportTo[Dest, Rot]):
                Print("Silently moved.")
```

## 4. Impact on Safety
- **Network Desync**: `teleporter_device` perfectly handles the server-to-client handoff, ensuring the camera resets smoothly. Raw `TeleportTo` can sometimes cause momentary camera snapping if not carefully masked with fade-to-black effects.
- **Momentum**: If a player falls into a raw `TeleportTo` volume, they might retain their downward velocity and die of fall damage upon arriving. The `teleporter_device` has an Editor setting to "Maintain Momentum: No".

## Verification Checklist
- [ ] `teleporter_device` is used when visual/audio feedback is desired.
- [ ] `.Teleport(Agent)` is used on the destination device.
- [ ] `FortCharacter.TeleportTo[]` is wrapped in an `if` block because it is failable.

---
name: verse-damage-volumes
description: Using mutator_zone_device to create custom damage volumes, buffs, or triggers when an agent enters or exits an area.
---

# Damage Volumes & Mutator Zones (UEFN Expert)

## Activation / When to Use
- Mandatory when creating areas that affect players (e.g., Lava, Poison Gas, Healing Springs, Capture Points).
- Trigger when you need to know if an Agent is currently inside a specific 3D space.

## 1. The `mutator_zone_device`
The Mutator Zone is the primary tool for spatial detection in UEFN. It broadcasts events when an `agent` enters or leaves its volume.

### Setup
```verse
using { /Fortnite.com/Devices }
using { /Fortnite.com/Characters }

my_zone_manager := class(creative_device):

    @editable
    PoisonZone : mutator_zone_device = mutator_zone_device{}

    # Array to track who is currently inside
    var PlayersInZone : []agent = array{}
```

## 2. Event Binding
Bind to `AgentEntersEvent` and `AgentExitsEvent`.

```verse
    OnBegin<override>()<suspends>:void=
        PoisonZone.AgentEntersEvent.Subscribe(OnPlayerEnter)
        PoisonZone.AgentExitsEvent.Subscribe(OnPlayerExit)
        
        # Start the damage loop in the background
        spawn{ DamageLoop() }

    OnPlayerEnter(Agent : agent):void=
        set PlayersInZone = PlayersInZone + array{Agent}

    OnPlayerExit(Agent : agent):void=
        # See Collections reference for RemoveFromArray logic
        set PlayersInZone = RemoveFromArray(Agent, PlayersInZone)
```

## 3. Applying Damage over Time (DoT)
Use a background loop that iterates over the tracked array.

```verse
    DamageLoop()<suspends>:void=
        loop:
            Sleep(1.0) # Tick every 1 second
            
            for (TargetAgent : PlayersInZone):
                if (FortChar := TargetAgent.GetFortCharacter[]):
                    # Deal 10 damage
                    FortChar.Damage(damage_args{ Amount := 10.0 })
```

## 4. Applying Instant Buffs (No Loop)
If you just want to give a speed boost when they enter.

```verse
    @editable
    SpeedBuff : movement_modulator_device = movement_modulator_device{}

    OnPlayerEnter(Agent : agent):void=
        # The movement modulator applies the effect instantly
        SpeedBuff.Activate(Agent)
```

## 5. Impact on Safety
- **Array Leaks**: If a player disconnects or dies while inside the zone, `AgentExitsEvent` might not fire depending on the exact engine state. It is critical to wrap operations inside the loop with `GetFortCharacter[]` (which fails safely if the agent is gone) to prevent crashes.
- **Performance**: A `mutator_zone_device` is much cheaper than running a distance check `Distance(Player, ZoneCenter)` on every player every frame. Always use Zones for spatial queries.

## Verification Checklist
- [ ] `mutator_zone_device` is used for spatial overlaps.
- [ ] `AgentEntersEvent` and `AgentExitsEvent` are both bound to maintain accurate arrays.
- [ ] Damage over Time relies on an array of tracked agents and a `loop` with a `Sleep`.
- [ ] `Damage()` is called securely via `GetFortCharacter[]`.

---
name: verse-character-elimination-events
description: Tracking global eliminations and damage via the elimination_manager_device and fort_character native events.
---

# Character Events & Eliminations (UEFN Expert)

## Activation / When to Use
- Mandatory when building kill-trackers, lifesteal mechanics, custom respawns, or registering when an AI dies.
- Trigger when you need to execute Verse logic the moment an `agent` takes damage or is eliminated.

## 1. The `elimination_manager_device` (Global Scope)
The safest and most robust way to track kills globally across the entire island (for humans and AI).

### Editor Setup
1. Place an `Elimination Manager` device.
2. Target Type: All (or specific teams).

### Verse Implementation
```verse
using { /Fortnite.com/Devices }
using { /Fortnite.com/Characters }

my_kill_manager := class(creative_device):

    @editable
    ElimManager : elimination_manager_device = elimination_manager_device{}

    OnBegin<override>()<suspends>:void=
        # Fires whenever anyone dies
        ElimManager.EliminatedEvent.Subscribe(OnEntityEliminated)

    OnEntityEliminated(Agent : ?agent):void=
        # The 'Agent' here is the VICTIM
        if (Victim := Agent?):
            Print("An entity was eliminated!")
```
*(Note: If you need to know WHO did the killing, check the `EliminationEvent` signature for your specific UEFN version. Some devices use a `device_ai_interaction_result` payload which contains both Source and Target).*

## 2. Native `fort_character` Events (Local Scope)
If you are managing a specific player or a custom NPC (via `npc_behavior`), you can listen to the character's physical events directly.

### The `EliminatedEvent()`
Fires when health reaches 0.

```verse
MonitorPlayer(Player : player):void=
    if (FortChar := Player.GetFortCharacter[]):
        # Subscribe natively
        FortChar.EliminatedEvent().Subscribe(OnPlayerDied)

OnPlayerDied(Result : elimination_result):void=
    # Result contains details about the death
    if (Killer := Result.Instigator?):
        Print("Player was killed by another agent!")
```

### The `DamagedEvent()`
Fires every time the character takes ANY damage. Highly performance sensitive!

```verse
MonitorDamage(Player : player):void=
    if (FortChar := Player.GetFortCharacter[]):
        FortChar.DamagedEvent().Subscribe(OnPlayerDamaged)

OnPlayerDamaged(Result : damage_result):void=
    Amount := Result.Amount
    if (Attacker := Result.Instigator?):
        Print("Took {Amount} damage from an attacker.")
```

## 3. Impact on Safety
- **Memory Leaks**: `DamagedEvent` and `EliminatedEvent` on `fort_character` return a `cancelable` subscription. If you bind these every time a player spawns, but don't cancel them when the player leaves, you create severe memory leaks. Always store the `cancelable` and call `.Cancel()` on disconnect.
- **Performance**: `DamagedEvent` fires on every tick of fire damage, storm damage, or rapid SMG hits. Do NOT put heavy logic (like searching arrays or spawning huge VFX) directly inside a `DamagedEvent` callback without a cooldown check.

## Verification Checklist
- [ ] `elimination_manager_device` is used for global score tracking.
- [ ] `fort_character.EliminatedEvent()` is used for specific, localized death tracking.
- [ ] Subscriptions to `fort_character` events are stored as `cancelable` to prevent leaks.
- [ ] `damage_result` or `elimination_result` structs are properly unwrapped to find the `Instigator`.

---
name: verse-cinematics-sequence
description: Controlling cinematics and cutscenes via cinematic_sequence_device. Playing, pausing, and passing agents to sequences.
---

# Cinematics & Sequencer (UEFN Expert)

## Activation / When to Use
- Mandatory when triggering cutscenes, camera animations, or environmental events (e.g., a door opening animation made in Sequencer).
- Trigger when you need to sync gameplay logic with a cinematic timeline.

## 1. The `cinematic_sequence_device`
This device acts as the bridge between Verse and the Level Sequence asset created in UEFN.

### Editor Setup
1. Create a Level Sequence in UEFN.
2. Place a `cinematic_sequence_device` in the level.
3. Assign the Level Sequence to the device.

### Verse Setup
```verse
using { /Fortnite.com/Devices }

my_cutscene_manager := class(creative_device):

    @editable
    MyCinematic : cinematic_sequence_device = cinematic_sequence_device{}
```

## 2. Playback Controls

### Playing globally vs Locally
- `Play()`: Plays the sequence for everyone in the server (if the device is configured to do so).
- `Play(Agent)`: If the cinematic is instanced/local (e.g., a first-person UI animation or a local camera cut), passing the Agent ensures it only plays for them.

```verse
    TriggerCutscene(Player : agent):void=
        # Play for this specific player
        MyCinematic.Play(Player)
```

### Stopping and Pausing
```verse
    # Pause the sequence at its current frame
    MyCinematic.Pause()

    # Stop and rewind to the beginning
    MyCinematic.Stop()
```

## 3. Synchronizing Logic (`Await`)
If you need Verse to wait until the cinematic finishes before giving the player their weapons back, use the `StoppedEvent`.

```verse
    PlayAndWait(Player : agent)<suspends>:void=
        Print("Cutscene starting...")
        MyCinematic.Play(Player)
        
        # Wait for the cinematic to finish
        MyCinematic.StoppedEvent.Await()
        
        Print("Cutscene finished! Giving weapons.")
        # GrantWeapons(Player)
```

## 4. Impact on Safety
- **Event Unreliability**: `StoppedEvent` fires when the sequence naturally ends OR if it is manually stopped. If you rely on `StoppedEvent` to grant a reward, ensure players can't skip or glitch the cinematic to trigger a stop early and farm rewards.
- **Instigator Tracking**: If a sequence moves the player's camera, always use `Play(Agent)` to ensure only the intended player's camera is hijacked, preventing griefing in multiplayer maps.

## Verification Checklist
- [ ] `cinematic_sequence_device` is used to reference the sequence.
- [ ] `Play(Agent)` is used for player-specific cutscenes to prevent global hijacking.
- [ ] `StoppedEvent.Await()` is used to sync Verse logic with cinematic completion.

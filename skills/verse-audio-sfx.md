---
name: verse-audio-sfx
description: Playing sounds dynamically using audio_player_device. Handling global vs local playback and spatialization.
---

# Audio & SFX (UEFN Expert)

## Activation / When to Use
- Mandatory when triggering sound effects, music, or voice-overs via code.
- Trigger when you need to play sounds for specific players or at specific moments.

## 1. The `audio_player_device`
Unlike Unreal C++, Verse cannot currently spawn raw Sound Waves or MetaSounds directly from a variable. You MUST use an `audio_player_device` placed in the level.

### Editor Setup
1. Place an `Audio Player` device.
2. Assign the Audio cue/asset in the Details panel.
3. **CRITICAL**: Set "Heard By" to *Everyone* (Global) or *Instigator Only* (Local).
4. Check *Enable Spatialization* if the sound should come from the device's 3D location.

## 2. Playback via Verse

```verse
using { /Fortnite.com/Devices }

my_audio_manager := class(creative_device):

    @editable
    LevelUpSound : audio_player_device = audio_player_device{}

    @editable
    BackgroundMusic : audio_player_device = audio_player_device{}
```

### Global vs Instigator Playback
- `Play()`: Plays the sound based on the device's default settings.
- `Play(Agent)`: Tells the device that this `Agent` is the Instigator. If the device is set to "Heard By: Instigator Only", ONLY this player will hear it.

```verse
    PlayerLeveledUp(Player : agent):void=
        # Only the player who leveled up hears the 'Ding!'
        LevelUpSound.Play(Player)

    StartBossFight():void=
        # Everyone hears the boss music
        BackgroundMusic.Play()
```

## 3. Spatialization (Moving Sounds)
If you need a sound to play at a specific location dynamically (e.g., an explosion where a projectile hit), you cannot pass a location to `Play()`.
**The Workaround**:
1. Attach the `audio_player_device` to a `creative_prop` in UEFN.
2. Move the prop using `TeleportTo` via Verse.
3. Call `Play()`.

```verse
    PlayExplosionAt(Location : vector3):void=
        # Assume AudioProp holds the AudioPlayer component
        if (AudioProp.TeleportTo[Location, IdentityRotation()]):
            ExplosionAudio.Play()
```

## 4. Impact on Safety
- **Audio Overlap**: Calling `Play()` 100 times in a loop will cause massive audio clipping and distortion. Use Verse timers or logic flags to prevent audio spam.
- **State Management**: If music is playing, use `Stop()` or `Stop(Agent)` to halt it before starting a new track, otherwise they will overlap.

## Verification Checklist
- [ ] Sounds are routed through `audio_player_device`.
- [ ] `Play(Agent)` is used for UI sounds or personal notifications (Requires "Instigator Only" in Editor).
- [ ] Spatialized sounds rely on moving the device's parent prop via `TeleportTo`.

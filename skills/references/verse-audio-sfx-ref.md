# Audio Reference

## 1. Managing Music Tracks
Switching between ambient and combat music.

```verse
@editable
AmbientMusic : audio_player_device = audio_player_device{}

@editable
CombatMusic : audio_player_device = audio_player_device{}

EnterCombat(Player : agent):void=
    # Stop ambient for this player
    AmbientMusic.Stop(Player)
    # Start combat for this player
    CombatMusic.Play(Player)

ExitCombat(Player : agent):void=
    CombatMusic.Stop(Player)
    AmbientMusic.Play(Player)
```

## 2. Fading Volume (Workaround)
Verse cannot directly lerp the volume of an audio device. To achieve fading, you must use multiple audio players or control the fade via a MetaSound parameter (if supported by future Verse updates) or Cinematic Sequencer.

Currently, the safest way to "fade" is to trigger a cinematic sequence that contains an audio track with a fade curve, rather than using the `audio_player_device`.

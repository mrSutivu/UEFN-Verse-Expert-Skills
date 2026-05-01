# Cinematics Reference

## 1. Advanced Playback Control
Controlling playback speed and reversing.

```verse
ReverseCutscene():void=
    # Set the play rate to negative
    MyCinematic.SetPlayRate(-1.0)
    MyCinematic.Play()

FastForward():void=
    # Play at 2x speed
    MyCinematic.SetPlayRate(2.0)
    MyCinematic.Play()
```

## 2. Syncing Multiple Devices to a Cinematic
When a cinematic finishes, you might want to open a door and spawn enemies simultaneously.

```verse
@editable
Cinematic : cinematic_sequence_device = cinematic_sequence_device{}
@editable
BossDoor : lock_device = lock_device{}
@editable
BossSpawner : npc_spawner_device = npc_spawner_device{}

OnBegin<override>()<suspends>:void=
    Cinematic.StoppedEvent.Subscribe(OnCinematicOver)

OnCinematicOver(Agent : agent):void=
    sync:
        BossDoor.Unlock(Agent)
        BossSpawner.Spawn()
```

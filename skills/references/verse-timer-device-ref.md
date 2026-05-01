# Timer Device Reference

## 1. Dynamic Timer Durations
You can override the editor's timer duration using Verse, allowing you to add or subtract time dynamically (e.g., picking up a "Time Extension" item).

```verse
@editable CountdownTimer : timer_device = timer_device{}

AddTime(SecondsToAdd : float):void=
    # Note: Modifying the time doesn't automatically start it if it was stopped.
    # Check the API for the exact duration methods available in the current Verse build.
    # Often handled by stopping, calculating new time, and starting.
    
    # Example (Pseudo-logic based on available UEFN features):
    # Depending on UEFN updates, you might need to use a tracker_device or 
    # specific timer_device functions like .AddTimeToTimer(Seconds) if exposed.
```

*(Note: As UEFN API updates frequently, always check if `.AddTimeToTimer()` or a similar function is exposed in `Fortnite.digest.verse`. If not, timers must be reset and started fresh, or logic must rely on custom Verse UI loops for highly dynamic clocks).*

## 2. Using Timer to Trigger Wave Spawns
```verse
@editable WaveTimer : timer_device = timer_device{}
@editable Spawner : npc_spawner_device = npc_spawner_device{}

OnBegin<override>()<suspends>:void=
    WaveTimer.SuccessEvent.Subscribe(OnWaveTimerFinished)
    WaveTimer.Start()

OnWaveTimerFinished(MaybeAgent : ?agent):void=
    # Timer hit 0
    Spawner.Spawn()
    
    # Restart for next wave
    WaveTimer.Reset()
    WaveTimer.Start()
```

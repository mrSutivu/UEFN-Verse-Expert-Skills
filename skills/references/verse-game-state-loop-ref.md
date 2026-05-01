# Game State Loop Reference

## 1. Round-Based Execution
If your island settings are set to 5 Rounds, the Verse `OnBegin` function will run **5 times** (once per round). The server resets completely between rounds unless you use persistent `weak_map`s.

```verse
OnBegin<override>()<suspends>:void=
    Print("--- NEW ROUND STARTED ---")
    
    # Re-initialize variables (Arrays are reset automatically)
    # Start loop
    RunMatchLoop()
```

## 2. Advanced Win Conditions (Team Score)
Checking a target score in a loop.

```verse
@editable ScoreTracker : tracker_device = tracker_device{}

WinConditionPhase()<suspends>:void=
    loop:
        # Wait for ANY player to score
        Agent := ScoreTracker.CompleteEvent.Await()
        
        # Check total team score (Assuming custom logic)
        TeamScore := GetTeamScore(Agent)
        if (TeamScore >= 50):
            # Break the loop, finishing this phase, which wins the 'race' in the master loop
            break 
            
        # If not 50, loop continues and awaits the next score event
```

## 3. Disabling Systems on Match End
Using `defer` to ensure cleanup when the match ends.

```verse
RunMatchLoop()<suspends>:void=
    # Start a spawner loop in the background
    branch:
        SpawnerLoop()
        
    # Wait for match to end
    Sleep(60.0)
    
SpawnerLoop()<suspends>:void=
    defer:
        # Guarantee this prints when the branch is cancelled by the end of RunMatchLoop
        Print("Spawner Loop terminated.")
        
    loop:
        MySpawner.Spawn()
        Sleep(5.0)
```

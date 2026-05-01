---
name: verse-game-state-loop
description: Building a master round-based game loop in Verse using structured concurrency and the end_game_device.
---

# Game State Loop (UEFN Expert)

## Activation / When to Use
- Mandatory when building the core "Game Manager" device for your island.
- Trigger when implementing match phases (Pregame, Warmup, Active, End) entirely in Verse.

## 1. The Core Architecture
A master game loop relies entirely on Verse's structured concurrency (`loop`, `race`, `sync`, `Sleep()`). It dictates the flow of time for the entire match.

## 2. Implementation Syntax

### Editor Setup
1. Create a `game_manager` Verse device.
2. Place an `end_game_device` to physically end the round/match and trigger the UI scoreboard.

### The Master Loop
```verse
using { /Fortnite.com/Devices }
using { /Verse.org/Simulation }

my_game_manager := class(creative_device):

    @editable EndGameDevice : end_game_device = end_game_device{}
    @editable CountdownTimer : timer_device = timer_device{}

    OnBegin<override>()<suspends>:void=
        # Start the master coroutine
        RunMatchLoop()

    RunMatchLoop()<suspends>:void=
        Print("PHASE 1: Warmup")
        Sleep(5.0) # Wait 5 seconds
        
        Print("PHASE 2: Active Match")
        # Run the match phase, waiting for either the timer to end or an early win condition
        race:
            MatchTimerPhase()
            WinConditionPhase()
            
        Print("PHASE 3: Match Over")
        # Find the winner (assuming we have a tracked winner agent)
        if (Winner := GetPlayspace().GetPlayers()[0]):
            EndGameDevice.Activate(Winner)
```

### The Phases
By using `race`, whichever phase finishes first ends the match.

```verse
    MatchTimerPhase()<suspends>:void=
        # Wait for the physical HUD timer to run out
        CountdownTimer.Start()
        CountdownTimer.SuccessEvent.Await()
        Print("Time ran out!")

    WinConditionPhase()<suspends>:void=
        # Wait for 30 eliminations, or whatever custom logic you have
        # Example: Waiting for a custom event
        MyBossKilledEvent.Await()
        Print("Boss defeated early!")
```

## 3. State Management (Enums)
Use an `enum` to track the current state if other devices need to query it.

```verse
game_state := enum:
    Warmup
    Active
    Ended

my_game_manager := class(creative_device):
    var CurrentState : game_state = game_state.Warmup
```

## 4. Impact on Safety
- **Centralization**: Managing the flow of time in a single Verse file using `<suspends>` is vastly superior to chaining 20 Delay nodes and Triggers in the UEFN editor. It prevents race conditions and makes the game flow readable.
- **End Game Authority**: Always use the `end_game_device.Activate(Winner)`. Attempting to simulate an end-game by teleporting everyone to a box and locking their input ignores standard Fortnite XP grants and matchmaking resolution.

## Verification Checklist
- [ ] A master `<suspends>` loop manages the phases using `Sleep` or event `.Await()`.
- [ ] `race` is used to pit win conditions against a master timeout.
- [ ] `end_game_device.Activate(Agent)` is used to finalize the round and display the Victory Royale screen.

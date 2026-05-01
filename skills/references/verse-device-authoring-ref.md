# Verse Device Authoring Reference

## 1. Complete Device Template

```verse
using { /Fortnite.com/Devices }
using { /Fortnite.com/Characters }
using { /Verse.org/Simulation }

game_manager_device := class(creative_device):

    # ---- Editor Properties ----
    @editable
    StartButton : button_device = button_device{}

    @editable
    EndGameDevice : end_game_device = end_game_device{}

    @editable
    MaxScore : int = 100

    # ---- Internal State ----
    var CurrentScore : int = 0

    # ---- Lifecycle ----
    OnBegin<override>()<suspends>:void=
        Print("Game Manager Initialized.")
        
        # Setup logic
        StartButton.InteractedWithEvent.Subscribe(OnStartButtonPressed)

    # ---- Custom Methods ----
    OnStartButtonPressed(Agent : agent):void=
        Print("Game Started by an Agent!")
        # Start game loop...
```

## 2. Using Arrays with `@editable`
Useful for managing multiple spawn pads or item granters.

```verse
    @editable
    PlayerSpawners : []player_spawner_device = array{}

    OnBegin<override>()<suspends>:void=
        # Iterate through all exposed spawners
        for (Spawner : PlayerSpawners):
            Spawner.SpawnedEvent.Subscribe(OnPlayerSpawned)
```

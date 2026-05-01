# Input Triggers Reference

## 1. Charge-Up Abilities (Hold and Release)
Combining `PressedEvent` and `ReleasedEvent` to calculate how long a button was held.

```verse
using { /Verse.org/Simulation }

charge_ability := class(creative_device):

    @editable ChargeInput : input_trigger_device = input_trigger_device{}
    
    # Store the start time for each player
    var ChargeStartTimes : weak_map(player, float) = map{}

    OnBegin<override>()<suspends>:void=
        for (P : GetPlayspace().GetPlayers()):
            ChargeInput.Register(P)
            
        ChargeInput.PressedEvent.Subscribe(OnPressed)
        ChargeInput.ReleasedEvent.Subscribe(OnReleased)

    OnPressed(Agent : agent):void=
        if (Player := player[Agent]):
            # Record the current game time
            CurrentTime := GetSimulationElapsedTime()
            if (set ChargeStartTimes[Player] = CurrentTime) {}

    OnReleased(Agent : agent):void=
        if (Player := player[Agent], StartTime := ChargeStartTimes[Player]):
            # Calculate duration
            Duration := GetSimulationElapsedTime() - StartTime
            
            if (Duration > 2.0):
                Print("Max Charge Attack!")
            else:
                Print("Weak Attack!")
```

## 2. Stun Mechanic (Unregistering)
Disabling a player's ability to use custom inputs.

```verse
ApplyStun(Player : player)<suspends>:void=
    # Prevent input
    ChargeInput.Unregister(Player)
    Print("Player Stunned!")
    
    Sleep(3.0)
    
    # Restore input
    ChargeInput.Register(Player)
    Print("Stun Ended.")
```

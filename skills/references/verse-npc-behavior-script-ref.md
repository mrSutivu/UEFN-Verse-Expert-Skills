# NPC Behavior Reference

## 1. Reacting to External Events
An NPC that waits for a button press to follow the player.

```verse
my_follower_npc := class(npc_behavior):

    @editable FollowButton : button_device = button_device{}

    OnBegin<override>()<suspends>:void=
        if (Navigatable := GetAgent[].GetFortCharacter[].GetNavigatable[]):
            
            # Wait for button press
            PlayerToFollow := FollowButton.InteractedWithEvent.Await()
            
            # Start following
            loop:
                # Create a target from an agent
                Target := MakeNavigationTarget(PlayerToFollow)
                
                # Move towards player
                Navigatable.NavigateTo(Target, ?MovementType := movement_types.Running)
                
                # Small pause before recalculating path
                Sleep(1.0)
```

## 2. Managing Focus
Telling the NPC what to look at.

```verse
SetFocus(FocusInterface : focus_interface, TargetAgent : agent):void=
    # Focus on a specific agent
    FocusInterface.MaintainFocus(TargetAgent)
    
ClearFocus(FocusInterface : focus_interface):void=
    # Return to looking straight ahead
    FocusInterface.ClearFocus()
```

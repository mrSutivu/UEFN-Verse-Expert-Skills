# Damage Volumes Reference

## 1. Array Removal Logic
Since Verse doesn't have a built-in `.Remove(Item)` yet, here is the exact snippet required for the `OnPlayerExit` logic.

```verse
# Custom helper function
RemoveAgentFromArray(Target : agent, OldArray : []agent):[]agent=
    var NewArray : []agent = array{}
    for (Item : OldArray, Item <> Target):
        set NewArray = NewArray + array{Item}
    return NewArray

# Usage in Exit Event
OnPlayerExit(Agent : agent):void=
    set PlayersInZone = RemoveAgentFromArray(Agent, PlayersInZone)
```

## 2. Capture Point Logic
Using a zone to calculate dominance (e.g., Team 1 vs Team 2).

```verse
var Team1Count : int = 0
var Team2Count : int = 0

OnPlayerEnter(Agent : agent):void=
    # Assuming you have a way to check teams
    if (GetTeam(Agent) = 1):
        set Team1Count += 1
    else:
        set Team2Count += 1
        
    EvaluateCaptureState()

OnPlayerExit(Agent : agent):void=
    if (GetTeam(Agent) = 1):
        set Team1Count -= 1
    else:
        set Team2Count -= 1
        
    EvaluateCaptureState()
```

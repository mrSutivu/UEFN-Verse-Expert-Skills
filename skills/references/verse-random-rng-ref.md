# Random Generation Reference

## 1. Picking a Random Player
Very common for "Infected" or "Juggernaut" game modes.

```verse
using { /Fortnite.com/Playspaces }
using { /Verse.org/Random }

SelectRandomPlayer():?player=
    AllPlayers := GetPlayspace().GetPlayers()
    
    if (AllPlayers.Length > 0):
        # Pick index
        RandomIndex := GetRandomInt(0, AllPlayers.Length - 1)
        
        # Failable access
        if (ChosenPlayer := AllPlayers[RandomIndex]):
            return option{ChosenPlayer}
            
    return false
```

## 2. Weighted Random (Loot Drops)
A simple implementation of weighted probability (e.g., 80% Common, 20% Legendary).

```verse
GetLootTier():string=
    Roll := GetRandomInt(1, 100)
    
    if (Roll <= 80):
        return "Common"
    else:
        return "Legendary"
```

## 3. Shuffling for Round-Robin
If you need to assign unique spawns to players without overlapping.

```verse
AssignSpawns(Players : []player, Spawns : []player_spawner_device):void=
    # Shuffle spawns so it's different every game
    RandomSpawns := Shuffle(Spawns)
    
    for (Index -> P : Players):
        # Safely get a unique spawn for this player
        if (AssignedSpawn := RandomSpawns[Index]):
            # Move player or configure spawner
            Print("Player assigned to a random spawn")
```

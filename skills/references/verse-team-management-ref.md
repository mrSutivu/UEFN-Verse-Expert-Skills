# Team Management Reference

## 1. Getting All Teams
Iterating through the global list of teams to find Team 1 and Team 2.

```verse
InitializeTeams():void=
    TeamCollection := GetPlayspace().GetTeamCollection()
    AllTeams : []team = TeamCollection.GetTeams()
    
    # Failable access
    if (Team1 := AllTeams[0]):
        Print("Got Team 1")
    if (Team2 := AllTeams[1]):
        Print("Got Team 2")
```

## 2. Iterating Players on a Team
Finding all players currently on Team 1 (e.g., to grant them all a point).

```verse
RewardWinningTeam(WinningTeam : team):void=
    TeamCollection := GetPlayspace().GetTeamCollection()
    
    # GetAgents returns an array of agents on that team
    TeamMembers : []agent = TeamCollection.GetAgents[WinningTeam]
    
    for (Member : TeamMembers):
        # Give reward...
        Print("Rewarding member.")
```

## 3. Infection Logic (1vAll)
Randomly picking an Alpha Zombie at the start of a round.

```verse
using { /Verse.org/Random }

@editable ZombieSelector : class_and_team_selector_device = class_and_team_selector_device{}

PickFirstZombie():void=
    AllPlayers := GetPlayspace().GetPlayers()
    
    if (AllPlayers.Length > 0):
        RandomIndex := GetRandomInt(0, AllPlayers.Length - 1)
        if (ChosenPlayer := AllPlayers[RandomIndex]):
            # Change their team to the Zombie team
            ZombieSelector.ChangeClassAndTeam(ChosenPlayer)
            Print("Zombie Selected!")
```

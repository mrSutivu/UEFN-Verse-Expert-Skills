---
name: verse-team-management
description: Handling teams using fort_team_collection and GetTeam(). Assigning, balancing, and comparing teams dynamically.
---

# Team Management (UEFN Expert)

## Activation / When to Use
- Mandatory for team-based game modes (Red vs Blue, Infected, Capture the Flag).
- Trigger when you need to assign a player to a specific team or check if two players are friendly.

## 1. The `fort_team_collection` Module
Teams in UEFN are managed by a global interface called `fort_team_collection`. You access it via the `playspace`.

### Required Imports
```verse
using { /Fortnite.com/Playspaces }
using { /Fortnite.com/Game } # For team type
```

## 2. Reading Team Data

### Checking a Player's Team
```verse
GetPlayerTeam(Player : player):?team=
    # 1. Get the global team collection
    TeamCollection := GetPlayspace().GetTeamCollection()
    
    # 2. Get the team (Returns an Option ?team)
    if (PlayerTeam := TeamCollection.GetTeam[Player]):
        return option{PlayerTeam}
        
    return false
```

### Are Players Friendly?
```verse
AreEnemies(Player1 : player, Player2 : player):logic=
    TeamCollection := GetPlayspace().GetTeamCollection()
    
    # IsOnSameTeam is failable if the players don't have teams
    if (TeamCollection.IsOnSameTeam[Player1, Player2]):
        return false # Friendly
    else:
        return true  # Enemies
```

## 3. Manipulating Teams (Class Selector)
While `fort_team_collection` provides read access, to **CHANGE** a player's team via Verse, you typically rely on the `class_and_team_selector_device`.

### Editor Setup
1. Place a `Class Selector` device.
2. Set "Team to Switch To" (e.g., Team 2).

### Verse Implementation
```verse
using { /Fortnite.com/Devices }

my_team_manager := class(creative_device):

    @editable SwitchToTeam2Device : class_and_team_selector_device = class_and_team_selector_device{}

    MakePlayerInfected(Player : player):void=
        # Instantly changes the player's team
        SwitchToTeam2Device.ChangeClassAndTeam(Player)
```

## 4. Impact on Safety
- **Option Unwrapping**: `GetTeam[]` is failable. Players might be spectators or in a lobby state where they do not have a defined team. Always wrap in an `if`.
- **Built-in Systems**: Relying on the `class_and_team_selector_device` ensures that when a player changes teams, Fortnite automatically handles respawning them (if configured), updating their outline colors, and voice chat channels. Doing this purely via raw math ignores these engine systems.

## Verification Checklist
- [ ] `GetPlayspace().GetTeamCollection()` is used for reading team data.
- [ ] `IsOnSameTeam[P1, P2]` is used for friendly-fire checks.
- [ ] `class_and_team_selector_device` is used to actively change a player's team.
- [ ] Team lookups are wrapped in failable `if` contexts.

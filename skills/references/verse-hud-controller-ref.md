# HUD Controller Reference

## 1. Cinematic UI Pattern
Hiding the UI during a sequence and guaranteeing it comes back.

```verse
using { /Fortnite.com/UI }
using { /Fortnite.com/Playspaces }

PlayCinematicMode(Player : player)<suspends>:void=
    HUDController := GetPlayspace().GetHUDController()
    AllUI := array{player_hud_identifier_all{}, creative_hud_identifier_all{}}
    
    # 1. Hide UI
    HUDController.HideElementsForPlayer(Player, AllUI)
    
    defer:
        # 3. GUARANTEE UI Restoration regardless of what happens
        HUDController.ShowElementsForPlayer(Player, AllUI)
        Print("UI Restored.")
        
    # 2. Play cinematic sequence
    MyCinematic.Play(Player)
    MyCinematic.StoppedEvent.Await()
```

## 2. Common HUD Identifiers
A quick reference of the most useful tags to hide:
- `player_hud_identifier_health{}`: Hides health and shield bars.
- `player_hud_identifier_equipped_item{}`: Hides the weapon/ammo corner.
- `player_hud_identifier_reticle{}`: Hides the crosshair.
- `creative_hud_identifier_minimap{}`: Hides the top-right minimap.
- `player_hud_identifier_building_resource{}`: Hides wood/brick/metal counts.

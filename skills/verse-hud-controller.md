---
name: verse-hud-controller
description: Hiding or modifying native Fortnite HUD elements (Minimap, Health, Reticle) using the Playspace HUD Controller API.
---

# HUD Controller API (UEFN Expert)

## Activation / When to Use
- Mandatory when creating immersive experiences where the standard Fortnite UI breaks the aesthetic (e.g., Horror games, Custom RPG menus).
- Trigger when you need to hide the Minimap, Health Bar, or Building resources dynamically.

## 1. The Core Principle
You can place a `hud_controller_device` in the level to hide elements globally, but controlling it dynamically via Verse provides vastly superior control per-player.
Verse accesses this via `GetPlayspace().GetHUDController()`.

## 2. Implementation Syntax

### Required Modules
```verse
using { /Fortnite.com/UI }
using { /Fortnite.com/Playspaces }
```

### 1. Hiding Elements Globally
```verse
HideDefaultUI():void=
    HUDController := GetPlayspace().GetHUDController()
    
    # Create an array of identifiers you want to hide
    ElementsToHide : []hud_element_identifier = array{
        player_hud_identifier_all{},      # Hides ALL base Fortnite UI
        creative_hud_identifier_all{}     # Hides ALL creative-specific UI (Score, Timer)
    }

    # Hide for everyone
    HUDController.HideElements(ElementsToHide)
```

### 2. Surgical Hiding (Specific Elements)
If you only want to hide the Minimap and the Reticle (Crosshair).

```verse
    SurgicalHide():void=
        HUDController := GetPlayspace().GetHUDController()
        
        SpecificElements : []hud_element_identifier = array{
            creative_hud_identifier_minimap{},
            player_hud_identifier_reticle{}
        }

        HUDController.HideElements(SpecificElements)
```

### 3. Targeting Specific Players
If a player enters a "Cinematic Mode", you only want to hide the UI for them.

```verse
    HideUIForPlayer(Player : player):void=
        HUDController := GetPlayspace().GetHUDController()
        
        ElementsToHide : []hud_element_identifier = array{player_hud_identifier_all{}}
        
        # Affects ONLY this player
        HUDController.HideElementsForPlayer(Player, ElementsToHide)
```

### 4. Restoring the UI
```verse
    RestoreUI(Player : player):void=
        HUDController := GetPlayspace().GetHUDController()
        
        ElementsToRestore : []hud_element_identifier = array{player_hud_identifier_all{}}
        
        # Brings the UI back
        HUDController.ShowElementsForPlayer(Player, ElementsToRestore)
```

## 3. Impact on Safety
- **Permanent Loss**: If you use `HideElements` and your code crashes or fails to call `ShowElements` before the round ends, the player might be stuck without a UI. Use `defer` if the hiding happens within a `<suspends>` block.
- **Weapon Reticles**: Hiding the `player_hud_identifier_reticle{}` is the ONLY reliable way to replace the Fortnite crosshair with a custom Verse Canvas crosshair.

## Verification Checklist
- [ ] `GetPlayspace().GetHUDController()` is used to access the API.
- [ ] `hud_element_identifier` array is properly configured using `{}` instantiation.
- [ ] `HideElementsForPlayer` is preferred over global `HideElements` for player-specific states.
- [ ] `ShowElements` or `ResetElements` is called to restore the UI when the custom state ends.

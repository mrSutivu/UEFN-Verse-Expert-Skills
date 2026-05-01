---
name: verse-save-point-device
description: Integrating Verse with the save_point_device. Triggering native Fortnite saves for inventory, position, and built-in stats.
---

# Save Point Device Integration (UEFN Expert)

## Activation / When to Use
- Mandatory when you need to save standard Fortnite data (Inventory, Gold, Position, Standard Score) across sessions.
- Trigger when `weak_map` persistence is too complex or unnecessary for the data you want to save.

## 1. Core Principles
While Verse has `<persistable> weak_map` for saving custom C++ style data (like RPG skill trees), Fortnite has a built-in `save_point_device` that automatically saves the player's physical state (Weapons, Ammo, Consumables, Transform).
- **The Catch**: The `save_point_device` is not natively exposed to Verse via an API like `MySavePoint.Save()`.

## 2. The Trigger Proxy Workflow
To command a Save Point from Verse, you must use `trigger_device`s as middlemen.

### Editor Setup
1. Place a `save_point_device` in the level. Configure what it saves (Inventory, Location, etc.).
2. Place two `trigger_device`s. Name them `Trigger_Save` and `Trigger_Load`.
3. Select the `save_point_device`. In the Details Panel under User Options - Functions:
   - **Save Player**: Add element -> Select `Trigger_Save` -> `On Triggered`.
   - **Load Player**: Add element -> Select `Trigger_Load` -> `On Triggered`.

### Verse Implementation
Now, Verse can trigger those proxies to activate the Save Point.

```verse
using { /Fortnite.com/Devices }

my_save_manager := class(creative_device):

    @editable
    TriggerSave : trigger_device = trigger_device{}

    @editable
    TriggerLoad : trigger_device = trigger_device{}

    # Called when player finishes a level
    CheckpointReached(Player : player):void=
        Print("Saving player state...")
        # Activating the trigger tells the Save Point to save this specific player
        TriggerSave.Trigger(Player)

    # Called when player joins
    OnPlayerJoined(Player : player):void=
        Print("Loading player state...")
        TriggerLoad.Trigger(Player)
```

## 3. Impact on Safety & Modularity
- **Data Integrity**: Using the native Save Point device for inventories is 100x safer than trying to manually track every bullet and weapon type in a Verse `weak_map`. Always let Fortnite handle standard Fortnite data.
- **Auto-Load Limitations**: If the Save Point is configured to "Auto Load" on spawn, you may not need the `Trigger_Load` proxy. However, having it linked to a Verse function allows you to delay the load (e.g., waiting for a cinematic to finish).

## Verification Checklist
- [ ] `save_point_device` is used for standard inventory and positional saving.
- [ ] `weak_map` persistence is used ONLY for custom variables (Level, Custom XP).
- [ ] `trigger_device`s are exposed via `@editable` to act as bridges to the Save Point.
- [ ] `.Trigger(Player)` is passed the correct `player` or `agent` to ensure the correct save slot is updated.

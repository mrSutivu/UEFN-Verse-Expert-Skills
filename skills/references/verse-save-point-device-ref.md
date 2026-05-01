# Save Point Device Reference

## 1. Hybrid Saving (Verse + Save Point)
The ultimate architecture for RPGs. Saving custom Verse stats alongside the Fortnite Inventory.

```verse
using { /Fortnite.com/Devices }

# 1. Custom Verse Persistence
player_rpg_data := class<persistable><final>:
    QuestStage : int = 0

var GlobalRPGData : weak_map(player, player_rpg_data) = map{}

my_save_manager := class(creative_device):

    # 2. Native Save Point Proxies
    @editable TriggerSaveInventory : trigger_device = trigger_device{}
    @editable TriggerLoadInventory : trigger_device = trigger_device{}

    # 3. The Master Save Function
    SaveEverything(Player : player, CurrentQuestStage : int):void=
        
        # A. Save Custom Data
        if (set GlobalRPGData[Player] = player_rpg_data{ QuestStage := CurrentQuestStage }):
            Print("Verse Custom Data Saved.")

        # B. Save Fortnite Inventory
        TriggerSaveInventory.Trigger(Player)
        Print("Fortnite Inventory Saved.")

    # 4. The Master Load Function
    LoadEverything(Player : player):void=
        
        # A. Load Custom Data
        if (LoadedData := GlobalRPGData[Player]):
            Print("Restored Quest Stage: {LoadedData.QuestStage}")
            
        # B. Load Fortnite Inventory
        TriggerLoadInventory.Trigger(Player)
```

## 2. Event Routing from Save Point
If you need Verse to know *when* a Save Point auto-loads a player successfully, you can route an event OUT of the Save Point.

1. Create `Trigger_OnLoadSuccess`.
2. In Save Point: `On Player Loaded` -> `Trigger_OnLoadSuccess.Trigger`.
3. In Verse:
```verse
    @editable Trigger_OnLoadSuccess : trigger_device = trigger_device{}

    OnBegin<override>()<suspends>:void=
        Trigger_OnLoadSuccess.TriggeredEvent.Subscribe(OnPlayerFullyLoaded)
        
    OnPlayerFullyLoaded(MaybeAgent : ?agent):void=
        if (Agent := MaybeAgent?):
            Print("Player inventory is now fully loaded.")
```

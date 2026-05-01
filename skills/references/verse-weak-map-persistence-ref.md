# Weak Map Reference

## 1. Complex Save Data Architecture
Because UEFN limits you to 4 persistent `weak_map`s, you must pack all your data into a single root class per player.

```verse
# The Sub-structures (Must also be persistable)
inventory_data := class<final><persistable>:
    Wood : int = 0
    Stone : int = 0

stats_data := class<final><persistable>:
    TotalKills : int = 0
    TotalDeaths : int = 0

# The Master Save Class
master_save_profile := class<final><persistable>:
    Inventory : inventory_data = inventory_data{}
    Stats : stats_data = stats_data{}
    IsVIP : logic = false

# The Single Global Map
var MasterDatabase : weak_map(player, master_save_profile) = map{}
```

## 2. Modifying Nested Save Data
Because Verse assigns by value/reconstruction for persistable data, updating a nested value requires rebuilding the tree.

```verse
AddWoodToPlayer(Player : player, Amount : int):void=
    if (Profile := MasterDatabase[Player]):
        
        # 1. Rebuild the Inventory
        NewInventory := inventory_data:
            Wood := Profile.Inventory.Wood + Amount
            Stone := Profile.Inventory.Stone
            
        # 2. Rebuild the Master Profile
        NewProfile := master_save_profile:
            Inventory := NewInventory
            Stats := Profile.Stats
            IsVIP := Profile.IsVIP
            
        # 3. Save
        if (set MasterDatabase[Player] = NewProfile) {}
```

---
name: verse-weak-map-persistence
description: Using weak_map for global session data and persistent player progress. Preventing memory leaks for disconnected players.
---

# Weak Map & Persistence (UEFN Expert)

## Activation / When to Use
- Mandatory when storing custom data (Score, Inventory, Level) for a specific `player`.
- Trigger when implementing Save Data (Persistence) across matches.

## 1. The Danger of Standard Maps for Players
If you store player data in a standard `var MyMap : [player]int = map{}`, and the player disconnects, your Map still holds a strong reference to that player. This causes a **Memory Leak**. The server cannot garbage collect the player.

## 2. The Solution: `weak_map`
A `weak_map` does not prevent its keys from being garbage collected. If a player leaves, their entry in the `weak_map` is safely invalidated.
- **Rule**: `weak_map` can ONLY be defined at the global module scope (outside of any `class`).
- **Limitation**: You CANNOT iterate (loop) over a `weak_map`, nor can you get its size (`Length`).

## 3. Session Data (Temporary)
Data that resets when the match ends. Indexed by the current Session.

```verse
# Defined OUTSIDE of your creative_device class!
var PlayerKills : weak_map(session, [player]int) = map{}

my_device := class(creative_device):

    AddKill(Player : player):void=
        CurrentSession := GetSession()
        
        # Read current kills (Option return)
        var CurrentKills : int = 0
        if (Kills := PlayerKills[CurrentSession][Player]):
            set CurrentKills = Kills
            
        # Update
        if (set PlayerKills[CurrentSession][Player] = CurrentKills + 1):
            Print("Kill tracked.")
```

## 4. Persistent Data (Save Games)
Data that survives when the player leaves the game and returns tomorrow.
- Requires marking the data class with `<final><persistable>`.
- Indexed directly by `player`.
- **Limit**: Maximum 4 persistent `weak_map`s per UEFN island.

```verse
# 1. Define the Save Data structure
player_save_data := class<final><persistable>:
    Level : int = 1
    Gold : int = 0

# 2. Define the Persistent Map globally
var GlobalSaveData : weak_map(player, player_save_data) = map{}

my_device := class(creative_device):

    GiveGold(Player : player, Amount : int):void=
        # 3. Read/Write
        if (PlayerData := GlobalSaveData[Player]):
            # Player exists in save file, update
            if (set GlobalSaveData[Player] = player_save_data{ Level := PlayerData.Level, Gold := PlayerData.Gold + Amount }):
                Print("Gold Saved!")
        else:
            # First time player, initialize
            if (set GlobalSaveData[Player] = player_save_data{ Level := 1, Gold := Amount }):
                Print("New Save Created!")
```

## 5. Impact on Safety
- **Server Health**: Using `weak_map` is the ONLY safe way to associate data with players in long-running servers.
- **Validation**: Always check `if (Player.IsActive[])` before reading/writing to ensure the player hasn't just disconnected.

## Verification Checklist
- [ ] `weak_map` is declared at the module scope (global).
- [ ] Standard `map` is avoided when the key is a `player` or `agent`.
- [ ] Data classes intended for save files are marked `<final><persistable>`.
- [ ] Accessing a `weak_map` unwraps the result using `if` or `or`.

---
name: verse-item-granter-device
description: Managing player loadouts natively using the item_granter_device in Verse. Granting, equipping, and clearing items.
---

# Item Granter Device (UEFN Expert)

## Activation / When to Use
- Mandatory when giving weapons, consumables, or resources to a player via Verse.
- Trigger when clearing a player's inventory during a round reset or class change.

## 1. The `item_granter_device`
Verse cannot spawn a "Pump Shotgun" directly into a player's hands via code. You must place an `item_granter_device` in the level, place the Shotgun inside it, and command it via Verse.

### Editor Setup
1. Place the `Item Granter` device.
2. Drop the desired items (Weapons, Ammo) onto the device.
3. Configure settings: "Grant Condition" (e.g., Always), "Clear Inventory" (Yes/No), "Equip Granted Item" (Yes/No).

## 2. Implementation Syntax

### Granting Items
```verse
using { /Fortnite.com/Devices }

my_loadout_manager := class(creative_device):

    @editable ShotgunGranter : item_granter_device = item_granter_device{}
    @editable AssaultRifleGranter : item_granter_device = item_granter_device{}

    GiveLoadout(Player : agent):void=
        # Grants the item to the specific agent
        ShotgunGranter.GrantItem(Player)
        AssaultRifleGranter.GrantItem(Player)
```

### Removing / Clearing Items
You can configure an Item Granter to drop or delete items. Alternatively, there is a dedicated `item_remover_device` if you want to remove specific items without granting new ones.

To clear an entire inventory via the Granter:
- Set the Granter's `Clear Inventory` setting to `Yes` in the Editor.
- Call `GrantItem`.

```verse
    ResetPlayer(Player : agent):void=
        # If configured to clear, this removes everything and gives the default item
        DefaultPistolGranter.GrantItem(Player)
```

## 3. Cycle and Next Item
If the granter holds multiple items (e.g., an array of random weapons), you can cycle through them.

```verse
    GiveRandomWeapon(Player : agent):void=
        # Moves to a random item in the granter's internal list
        RandomGranter.CycleToRandomItem(Player)
        RandomGranter.GrantItem(Player)
```

## 4. Impact on Safety
- **Validation**: `GrantItem` takes an `agent`. If you have a `fort_character` or a `player`, they both count as `agent`s and can be passed directly to the function without casting.
- **Inventory Overflows**: Relying on the Granter's Editor settings ("Keep All", "Drop", "Delete") ensures that granting an item when the inventory is full behaves exactly as standard Fortnite logic dictates, preventing physics crashes from dropped loot.

## Verification Checklist
- [ ] `item_granter_device` is used instead of attempting to spawn raw asset references.
- [ ] `.GrantItem(Agent)` is called to deliver the payload.
- [ ] Inventory clearing logic is configured in the Editor Details panel of the Granter, not via complex Verse loops.

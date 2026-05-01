# Verse Tag Markup Reference

## 1. Building an Array from a Tag Generator
If you absolutely need an array (e.g., to pick a random object), you can construct it from the generator.

```verse
using { /Verse.org/Random }

GetRandomSpawner():?player_spawner_device=
    var Spawners : []player_spawner_device = array{}
    
    # 1. Build the array
    for (Obj : FindCreativeObjectsWithTag(my_spawner_tag{}), Spawner := player_spawner_device[Obj]):
        set Spawners = Spawners + array{Spawner}
        
    # 2. Pick random
    if (Spawners.Length > 0):
        RandomIndex := GetRandomInt(0, Spawners.Length - 1)
        if (Chosen := Spawners[RandomIndex]):
            return option{Chosen}
            
    return false
```

## 2. Multi-Tag Filtering
Finding objects that match specific criteria by nesting casts.

```verse
# Assuming you tagged various objects with 'interactive_tag'
for (Obj : FindCreativeObjectsWithTag(interactive_tag{})):
    
    # Is it a button?
    if (Button := button_device[Obj]):
        Button.Enable()
        
    # Or is it a switch?
    else if (Switch := switch_device[Obj]):
        Switch.TurnOn()
```

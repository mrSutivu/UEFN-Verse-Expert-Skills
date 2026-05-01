---
name: verse-tag-markup
description: Using Gameplay Tags and FindCreativeObjectsWithTag to query actors and devices dynamically in Verse.
---

# Verse Tag Markup (UEFN Expert)

## Activation / When to Use
- Mandatory when you need to find multiple devices or props in the level without linking them all manually via `@editable` arrays.
- Trigger when building dynamic systems (e.g., finding all "Capture Points" or "Spawn Locations").

## 1. Core Principles
Instead of hard-wiring 50 light switches to your Manager script, you give those switches a Tag in the UEFN Editor. Your Verse script then searches the world for that Tag.
- **Performance**: Faster and much more scalable than `@editable` arrays for large counts.
- **Deprecated Function**: `GetCreativeObjectsWithTag` is DEAD. You MUST use `FindCreativeObjectsWithTag`.

## 2. Implementation Syntax

### 1. Define the Tag in Verse
Create a new class inheriting from `tag`.

```verse
using { /Verse.org/Simulation/Tags }

# Define your custom tag
my_light_tag := class(tag){}
my_spawner_tag := class(tag){}
```

### 2. Assign the Tag in UEFN
1. Select the Device/Prop in the Editor.
2. Add a `Verse Tag Markup` component.
3. Add your compiled tag (`my_light_tag`) to the component's list.

### 3. Querying the Tag
`FindCreativeObjectsWithTag` returns a **Generator**, not an array. It must be used directly inside a `for` loop.

```verse
using { /Fortnite.com/Devices }

TurnOffAllLights():void=
    # 1. Query the generator using the tag instantiation {}
    for (TaggedObject : FindCreativeObjectsWithTag(my_light_tag{})):
        
        # 2. Cast the generic object to the specific device type you expect
        if (LightDevice := customizable_light_device[TaggedObject]):
            LightDevice.TurnOff()
```

## 3. Impact on Safety & Modularity
- **Level Design Freedom**: Designers can duplicate, delete, and move tagged objects in the editor freely without ever breaking the Verse script's `@editable` references.
- **Generator Efficiency**: Because it returns a Generator, the memory footprint is tiny. It processes objects one by one instead of allocating a massive array of 1,000 objects before looping.

## Common Mistakes (BAD vs GOOD)

**BAD (Using the Array Function)**:
```verse
# DEPRECATED AND SLOW
MyArray := GetCreativeObjectsWithTag(my_tag{}) 
```

**BAD (Missing Cast)**:
```verse
for (Obj : FindCreativeObjectsWithTag(my_tag{})):
    Obj.TurnOff() # COMPILER ERROR: Obj is a generic 'creative_object_interface'.
```

**GOOD (Generator + Cast)**:
```verse
for (Obj : FindCreativeObjectsWithTag(my_tag{}), SpecificObj := my_device[Obj]):
    SpecificObj.DoAction() # SAFE
```

## Verification Checklist
- [ ] Custom tags are declared as `class(tag){}`.
- [ ] `FindCreativeObjectsWithTag` is used (NOT `GetCreative...`).
- [ ] The query is used directly inside a `for` loop (Generator pattern).
- [ ] The `TaggedObject` is cast to the expected device type using `device_type[TaggedObject]`.

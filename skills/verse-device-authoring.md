---
name: verse-device-authoring
description: Creating custom UEFN devices in Verse using creative_device. Exposing properties to the Editor via @editable.
---

# Verse Device Authoring (UEFN Expert)

## Activation / When to Use
- Mandatory when writing Verse code that interacts with the Fortnite game world.
- Trigger when you need to expose variables or object references to the UEFN Details Panel.

## 1. The `creative_device` Base Class
Every Verse script that you drag and drop into the UEFN level must inherit from `creative_device`.

```verse
using { /Fortnite.com/Devices }
using { /Verse.org/Simulation }

my_custom_device := class(creative_device):

    # Triggered when the game starts
    OnBegin<override>()<suspends>:void=
        Print("Hello UEFN!")
```

## 2. Exposing Properties (`@editable`)
To link Fortnite objects (like a Button or a Spawner) to your Verse script, use the `@editable` attribute. This acts like `UPROPERTY(EditAnywhere)` in C++.

```verse
my_custom_device := class(creative_device):

    # Exposes a button reference to the UEFN Editor
    @editable
    MyButton : button_device = button_device{}

    # Exposes a configurable float
    @editable
    GameDuration : float = 60.0

    # Exposes an array of spawners
    @editable
    Spawners : []item_spawner_device = array{}
```

## 3. The `OnBegin` Lifecycle
`OnBegin` is the entry point. It is marked `<suspends>`, meaning you can immediately start asynchronous loops or `race` blocks inside it.

## 4. Impact on Safety
- **Data Binding**: By instantiating the default device `button_device{}` in the code, the editor knows exactly what type to look for. If the designer deletes the button in UEFN, Verse handles the missing reference safely (usually via Option types if dynamically fetched, though `@editable` assumes static linking).
- **Architecture**: A level should have Manager devices (`game_manager_device`) that hold references to many smaller devices, centralizing the logic in Verse rather than relying on messy Blueprint wire spaghetti.

## Verification Checklist
- [ ] Class inherits from `creative_device`.
- [ ] Properties needed by level designers are marked `@editable`.
- [ ] `@editable` variables are given default instantiations (e.g., `= 0.0` or `= button_device{}`).
- [ ] Execution logic starts inside `OnBegin<override>()<suspends>`.

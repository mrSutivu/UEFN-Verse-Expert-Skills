---
name: verse-input-triggers
description: Capturing custom keybinds and controller inputs natively using the input_trigger_device.
---

# Input Triggers (UEFN Expert)

## Activation / When to Use
- Mandatory when creating custom abilities triggered by a specific button (e.g., "Press 'F' to Dash" or "Hold 'Space' to Fly").
- Trigger when you need to intercept standard inputs (Jump, Crouch, Fire) and run Verse logic instead.

## 1. The `input_trigger_device`
Verse cannot globally listen to keyboard presses. You must place an `Input Trigger Device` in the level.
- In the Editor, configure the device to listen to a specific action (e.g., `Jump`, `Crouch`, or custom `Creative Input 1-12`).
- You can configure if the input is **Consumed** (e.g., if you map Dash to 'Jump' and Consume it, the player will Dash but will *not* jump).

## 2. Implementation Syntax

### 1. The Crucial Step: Registration
An Input Trigger does absolutely nothing until a player is explicitly **Registered** to it via Verse.

```verse
using { /Fortnite.com/Devices }

my_ability_manager := class(creative_device):

    @editable
    DashInput : input_trigger_device = input_trigger_device{}

    OnBegin<override>()<suspends>:void=
        # 1. Register existing players
        for (Player : GetPlayspace().GetPlayers()):
            DashInput.Register(Player)
            
        # 2. Subscribe to the event
        DashInput.PressedEvent.Subscribe(OnDashPressed)
```

### 2. Handling the Event
```verse
    OnDashPressed(Agent : agent):void=
        Print("Player pressed the Dash button!")
        # Execute ability logic here
```

### 3. Handling Late Joiners
If a player joins mid-game, they must be registered, or their buttons won't work.

```verse
    OnBegin<override>()<suspends>:void=
        # Listen for new players
        GetPlayspace().PlayerAddedEvent().Subscribe(OnPlayerAdded)

    OnPlayerAdded(Player : player):void=
        # Register the new player to the input
        DashInput.Register(Player)
```

## 3. Impact on Safety
- **Unregistering**: If you want to disable an ability temporarily (e.g., Silence/Stun), you can use `DashInput.Unregister(Player)` or `DashInput.Disable()`.
- **Creative Inputs vs Standard**: Using `Creative Input 1` allows players to rebind the key in their Fortnite Settings. Overriding standard inputs (`Jump`) is powerful but can confuse players if not communicated via the HUD.

## Verification Checklist
- [ ] `input_trigger_device` is used to capture button presses.
- [ ] `.Register(Player)` is explicitly called for all players (including late joiners).
- [ ] `.PressedEvent` or `.ReleasedEvent` are used to execute logic.
- [ ] The Editor settings dictate whether the input is consumed or allowed to pass through to the core game.

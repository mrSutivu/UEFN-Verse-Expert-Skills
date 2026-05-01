---
name: verse-ui-canvas-custom
description: Creating custom HUDs using Verse UI. Generating canvases, adding buttons, and routing widget_messages.
---

# Verse UI & Canvas (UEFN Expert)

## Activation / When to Use
- Mandatory when creating custom on-screen menus, scoreboards, or interactive HUDs for players in UEFN.
- Trigger when standard HUD Message devices are insufficient.

## 1. The Core UI Principles
Verse UI uses a declarative syntax built around the `canvas` widget.
- You must retrieve a player's `player_ui` object to draw on their screen.
- UI elements require specific modules: `UI` and `SpatialMath`.
- Text in UI requires the `message` type, using `set_text("String")`.

## 2. Implementation Syntax

### 1. Required Modules
```verse
using { /UnrealEngine.com/Temporary/UI }
using { /UnrealEngine.com/Temporary/SpatialMath }
using { /Fortnite.com/UI }
```

### 2. Creating the Canvas and Button
```verse
my_ui_device := class(creative_device):

    # 1. Create a button. Requires 'message' type text.
    var MyButton : button_loud = button_loud{ DefaultText := set_text("Click Me") }
    var MyCanvas : ?canvas = false

    OnBegin<override>()<suspends>:void=
        # Get Player UI
        if (Player := GetPlayspace().GetPlayers()[0], PlayerUI := GetPlayerUI[Player]):
            
            # 2. Build the Canvas
            NewCanvas := canvas:
                Slots := array:
                    canvas_slot:
                        # Center of screen
                        Anchors := anchors{ Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5} }
                        Alignment := vector2{X:=0.5, Y:=0.5}
                        Widget := MyButton
            
            set MyCanvas = option{NewCanvas}

            # 3. Add to screen WITH InputMode All (Enables Mouse)
            PlayerUI.AddWidget(NewCanvas, player_ui_slot{ InputMode := ui_input_mode.All })

            # 4. Subscribe to Click
            MyButton.OnClick().Subscribe(HandleClick)
```

### 3. The `widget_message` Callback
When a UI interaction happens, the callback receives a `widget_message` struct containing the `Player` and the `Source` widget.

```verse
    HandleClick(Message : widget_message):void=
        if (Player := Message.Player):
            Print("Player clicked the UI!")
            
            # Optional: Remove the UI after click
            if (PlayerUI := GetPlayerUI[Player], ActiveCanvas := MyCanvas?):
                PlayerUI.RemoveWidget(ActiveCanvas)
```

## 3. Impact on Safety
- **Mouse Lock**: If you call `AddWidget` without specifying `ui_input_mode.All`, the UI will appear, but the player's mouse will remain locked to their character camera, making the button unclickable.
- **Reference Tracking**: If you don't store `MyCanvas` in an option variable (`var MyCanvas : ?canvas`), you will not be able to pass it to `RemoveWidget()` later, resulting in permanent UI stuck on screen.

## Verification Checklist
- [ ] `button_loud` or `button_quiet` are instantiated with `set_text()`.
- [ ] `GetPlayerUI[Player]` is wrapped in a failable `if` context.
- [ ] `AddWidget` uses `ui_input_mode.All` if interaction is required.
- [ ] Callback function accepts `(Message : widget_message)`.
- [ ] Canvas reference is stored in a `var` to allow future removal.

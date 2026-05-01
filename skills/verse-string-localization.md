---
name: verse-string-localization
description: Formatting text in Verse. Differences between string and message types, interpolation, and localization requirements.
---

# String Localization & Messages (UEFN Expert)

## Activation / When to Use
- Mandatory when displaying text to the player (HUD, Billboards, Objective Text).
- Trigger when concatenating variables into a readable format.

## 1. `string` vs `message`
- **`string`**: A raw array of characters. Good for internal logic, map keys, or debug prints (`Print("Hello")`).
- **`message`**: A localizable text object. Fortnite requires this for any text rendered on the screen so it can interface with the localization/translation engine.

## 2. Interpolation (Formatting)
You CANNOT use `+` to concatenate `message` types or to combine `string` and `int`. You must use String Interpolation `{}`.

### Defining a Message
Messages must be defined at the module or class scope with the `<localizes>` specifier. You cannot define them inside a function.

```verse
# At class scope
my_ui_device := class(creative_device):

    # A simple localized message
    GreetingText<localizes> : message = "Welcome to the Arena!"

    # A parameterized message
    ScoreText<localizes>(PlayerName : string, Score : int) : message = "{PlayerName}, your score is: {Score}"
```

### Using a Message
Call the message definition like a function.

```verse
    UpdateUI(Player : player, CurrentScore : int):void=
        # Assuming we have a way to get the player's name as a string
        PName : string = "Gamer123" 
        
        # Format the message
        FormattedMsg := ScoreText(PName, CurrentScore)
        
        # Apply to a HUD device
        MyHUDMessageDevice.SetText(FormattedMsg)
        MyHUDMessageDevice.Show(Player)
```

## 3. The `StringToMessage` Utility
Often, you receive a raw `string` (e.g., from an `@editable` field) but need to pass a `message` to a UI function like `SetText()`.

```verse
    @editable 
    RawStringFromEditor : string = "Default String"

    # Utility function to convert string to message
    StringToMessage<localizes>(S : string) : message = "{S}"

    ApplyString():void=
        Msg := StringToMessage(RawStringFromEditor)
        MyHUDMessageDevice.SetText(Msg)
```

## 4. Impact on Safety
- **Concatenation Errors**: Trying to do `Msg1 + Msg2` will cause compiler errors. Always use a wrapper `<localizes>` function that takes `(M1:message, M2:message)` and returns `"{M1} {M2}"`.
- **Scope Errors**: Trying to define `MyMsg<localizes> : message = "Test"` inside `OnBegin` will fail. Move it to the class level.

## Verification Checklist
- [ ] Player-facing text uses the `message` type, not `string`.
- [ ] `message` variables are defined with `<localizes>`.
- [ ] `<localizes>` definitions are placed at the class or module level, not inside functions.
- [ ] Interpolation `{Var}` is used instead of concatenation `+`.

# String Localization Reference

## 1. Combining Messages
If you need to stitch two messages together dynamically.

```verse
# Class level definitions
CombineMessages<localizes>(M1 : message, M2 : message) : message = "{M1} - {M2}"

PrefixWarning<localizes> : message = "WARNING"
PlayerLeft<localizes>(Name : string) : message = "{Name} has disconnected."

# Inside a function
ShowWarning(PlayerName : string):void=
    LeftMsg := PlayerLeft(PlayerName)
    FinalMsg := CombineMessages(PrefixWarning, LeftMsg)
    
    # FinalMsg results in "WARNING - [Name] has disconnected."
    MyHUD.SetText(FinalMsg)
```

## 2. Float Formatting (Rounding)
Verse doesn't have robust string formatting (like `%.2f`) built-in natively for interpolation yet. If you interpolate a float directly `{MyFloat}`, it may show many decimal places. 
You must use math functions (like `Floor` or `Ceil`) to convert it to an `int` if you want a clean display.

```verse
ShowTimer(TimeRemaining : float):void=
    # Convert float to int for clean display
    if (CleanTime := Int[Floor[TimeRemaining]]):
        Msg := TimerMsg(CleanTime)
        MyHUD.SetText(Msg)
```

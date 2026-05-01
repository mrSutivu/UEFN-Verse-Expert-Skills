# Defer Execution Reference

## 1. UI Cleanup Pattern
The standard pattern for showing a UI screen with a timeout or external cancellation.

```verse
ShowWarningUI(Player : player)<suspends>:void=
    if (PlayerUI := GetPlayerUI[Player]):
        
        # 1. Create and Add UI
        MyWarningCanvas := CreateWarningCanvas()
        PlayerUI.AddWidget(MyWarningCanvas)
        
        # 2. Guarantee Cleanup
        defer:
            PlayerUI.RemoveWidget(MyWarningCanvas)
            
        # 3. Wait for 5 seconds. 
        # When Sleep finishes, the block ends, and defer fires.
        Sleep(5.0)
```

## 2. Order of Execution (LIFO)
Understanding multiple defers.

```verse
NestedDefers():void=
    defer:
        Print("This prints LAST (1)")
        
    defer:
        Print("This prints SECOND (2)")
        
    Print("This prints FIRST (3)")
```

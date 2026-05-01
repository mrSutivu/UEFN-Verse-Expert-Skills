# Prop Manipulation Reference

## 1. Moving Multiple Props Simultaneously
If you call `MoveTo` sequentially, Prop B waits for Prop A to finish. Use `sync` to move them together.

```verse
OpenDoubleDoors()<suspends>:void=
    LeftPos := LeftDoor.GetTransform().Translation + vector3{X := -200.0}
    RightPos := RightDoor.GetTransform().Translation + vector3{X := 200.0}
    
    # Move both at the same time
    sync:
        LeftDoor.MoveTo(LeftPos, LeftDoor.GetTransform().Rotation, 1.5)
        RightDoor.MoveTo(RightPos, RightDoor.GetTransform().Rotation, 1.5)
        
    Print("Both doors are open.")
```

## 2. Looping a Movement (Ping-Pong)
Creating a moving platform.

```verse
PlatformLoop()<suspends>:void=
    StartPos := Platform.GetTransform().Translation
    EndPos := StartPos + vector3{Z := 500.0}
    
    loop:
        # Move up
        Platform.MoveTo(EndPos, Platform.GetTransform().Rotation, 3.0)
        Sleep(1.0) # Pause at top
        
        # Move down
        Platform.MoveTo(StartPos, Platform.GetTransform().Rotation, 3.0)
        Sleep(1.0) # Pause at bottom
```

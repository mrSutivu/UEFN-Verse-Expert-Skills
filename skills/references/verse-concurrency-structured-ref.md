# Verse Concurrency Reference

## 1. The Classic Timeout Pattern (Race)
This is the most critical pattern in Verse. Waiting for a user input, but timing out after X seconds to prevent soft-locking the game.

```verse
WaitForPlayerAction()<suspends>:void=
    race:
        # Task 1: Wait for a button device event
        MyButton.InteractedWithEvent.Await()
        
        # Task 2: The timeout
        Sleep(15.0)
        
    Print("Action completed or timed out!")
```

## 2. Looping with Suspends
A background loop that yields every frame or specific interval.

```verse
GameLoop()<suspends>:void=
    loop:
        # Do logic
        UpdateCustomScore()
        
        # Yield to engine. (Sleep(0.0) waits 1 tick)
        Sleep(1.0)
```

## 3. Combining sync and race
Waiting for multiple conditions with a master timeout.

```verse
ComplexWait()<suspends>:void=
    race:
        Sleep(60.0) # Global 60s timeout
        sync:
            Button1.InteractedWithEvent.Await()
            Button2.InteractedWithEvent.Await()
            
    # Reached if both buttons pressed, OR 60 seconds passed.
```

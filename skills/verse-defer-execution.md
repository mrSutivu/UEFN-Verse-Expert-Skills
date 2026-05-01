---
name: verse-defer-execution
description: Using defer to guarantee cleanup of states, audio, or UI when a scope exits or a concurrent task is cancelled.
---

# Defer Execution & Cleanup (UEFN Expert)

## Activation / When to Use
- Mandatory when acquiring a resource (like spawning UI, playing a sound, or taking lock ownership) that MUST be cleaned up.
- Trigger when you need to guarantee code execution even if the function is cancelled by a `race` block or fails.

## 1. Core Principles
The `defer` block schedules code to run at the **very end** of the current scope.
- It runs whether the scope finishes normally, fails, returns early, or is aborted by a concurrent expression.
- Multiple `defer` blocks execute in reverse order (LIFO - Last In, First Out).

## 2. Implementation Syntax

### Simple Cleanup
```verse
ProcessPlayer(Player : player):void=
    Print("Starting process...")
    
    defer:
        # This will always print last, right before the function exits
        Print("Cleaning up process...")
        
    # Main logic
    Print("Processing...")
    # If the function returns here, the defer still runs.
```

### Concurrent Cleanup (Crucial)
When used inside a `race`, `defer` is the ONLY way to guarantee cleanup if the task loses the race.

```verse
using { /Fortnite.com/Devices }

my_cinematic_manager := class(creative_device):

    @editable MyCinematic : cinematic_sequence_device = cinematic_sequence_device{}
    @editable SkipButton : button_device = button_device{}

    PlayCinematicWithSkip(Player : player)<suspends>:void=
        race:
            # Task 1: Play cinematic normally
            block:
                MyCinematic.Play(Player)
                
                # GUARANTEE the cinematic stops if skipped
                defer:
                    MyCinematic.Stop(Player)
                    Print("Cinematic sequence stopped/cleaned up.")
                    
                MyCinematic.StoppedEvent.Await()
                
            # Task 2: Skip button
            SkipButton.InteractedWithEvent.Await()
            
        Print("Cinematic phase over.")
```
*If Task 2 (Button) wins the race, Task 1 is aborted. BUT, the `defer` inside Task 1 still executes, safely stopping the cinematic!*

## 3. Impact on Safety
- **Zombie State Prevention**: Without `defer`, if a coroutine that displays a UI canvas is aborted by a timeout, the UI will be stuck on the screen forever. `defer: PlayerUI.RemoveWidget(MyCanvas)` guarantees removal.
- **Transaction Rollback**: While `<transacts>` rolls back variables, `defer` is used for rolling back *side effects* (like playing audio or spawning props).

## Verification Checklist
- [ ] `defer` is used immediately after initializing a temporary UI, Audio, or Prop.
- [ ] Cleanup logic relies on `defer` rather than manually duplicating cleanup code at every `return` statement.
- [ ] `defer` is used inside `race` blocks to clean up the losing branches.

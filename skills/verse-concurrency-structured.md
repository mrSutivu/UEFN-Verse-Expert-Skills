---
name: verse-concurrency-structured
description: Mastering Verse concurrency using <suspends>, sync, race, rush, branch, and spawn. Managing async execution safely.
---

# Verse Structured Concurrency (UEFN Expert)

## Activation / When to Use
- Mandatory when dealing with time (Delays, Timers).
- Trigger when waiting for events (Player joined, Button pressed) without freezing the game.

## 1. The `<suspends>` Specifier
Any function that takes time to complete MUST be marked with `<suspends>`.
- **Rule**: A `<suspends>` function can only be called by another `<suspends>` function (or launched via `spawn`).

## 2. Structured Primitives
Verse builds concurrency into the language block level. 

### `sync` (Wait for All)
Executes all expressions simultaneously. Waits for ALL to finish before continuing.
```verse
sync:
    PlayAnimationA() # 2 seconds
    PlayAnimationB() # 5 seconds
# Code resumes after 5 seconds.
```

### `race` (First Wins, Cancel Others)
Executes simultaneously. When the FIRST expression finishes, it **aborts/cancels** the others instantly.
```verse
race:
    AwaitButtonPress() # Waits infinitely
    Sleep(10.0)        # 10 second timeout
# Code resumes when button is pressed OR 10s elapses. The loser is cancelled.
```

### `rush` (First Wins, Let Others Run)
Executes simultaneously. When the FIRST finishes, the main code continues, but the others keep running in the background.

### `branch` (Fire and Forget in Scope)
Starts a block in the background and immediately moves to the next line.
*Safety constraint*: If the function containing the `branch` ends, the `branch` is cancelled!

```verse
OnBegin()<suspends>:void=
    branch:
        LoopFX() # Cancelled when OnBegin finishes!
    DoSetup()
```

## 3. Unstructured: `spawn`
Use `spawn` to launch a `<suspends>` function that must survive beyond the lifespan of the current function.
```verse
spawn{ RunGameLoop() }
```

## 4. Impact on Safety
- **No Callback Hell**: Structured concurrency eliminates the need for complex C++ delegates or Blueprint delay chains.
- **Automatic Cleanup**: `race` automatically cancels hanging tasks (like a waiting event listener if a timer runs out), preventing memory leaks and zombie listeners.

## Verification Checklist
- [ ] Functions utilizing `Sleep` or waiting on events have `<suspends>`.
- [ ] `race` is used for implementing timeouts on events.
- [ ] `branch` is used for scoped background tasks, `spawn` for global tasks.

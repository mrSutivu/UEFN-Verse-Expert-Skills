---
name: verse-custom-events-signal
description: Using Verse's native event() class for async signaling and simulating custom subscriptions.
---

# Custom Events & Signals (UEFN Expert)

## Activation / When to Use
- Mandatory when you need coroutines to communicate with each other (e.g., a GameLoop waiting for a SpawnerLoop to finish).
- Trigger when you want to build a custom `listenable` system for your own Verse classes.

## 1. The Native `event()` Class
Verse provides `event` specifically for asynchronous coordination.
- **Signal()**: Triggers the event.
- **Await()**: Pauses execution until the event is signaled.

### Syntax
```verse
# Event with no data
var MyTriggerEvent : event() = event(){}

# Event passing an Integer
var MyDataEvent : event(int) = event(int){}
```

### Usage (Concurrency)
```verse
OnBegin<override>()<suspends>:void=
    branch:
        WaitForMyEvent() # Runs in background
        
    Sleep(2.0)
    MyTriggerEvent.Signal() # Unpauses the background task

WaitForMyEvent()<suspends>:void=
    MyTriggerEvent.Await()
    Print("Event Received!")
```

## 2. The Subscription Limitation
**CRITICAL**: The native `event()` class DOES NOT support `.Subscribe()`. It only supports `.Await()`.
If you try to write `MyEvent.Subscribe(Callback)`, the compiler will fail.

## 3. Creating a "Custom Listenable" Wrapper
If you are building an API that other designers/programmers will use, and you want them to be able to `.Subscribe()` to your custom events like they do with Fortnite Devices, you must build a Wrapper.

*Note: See the Reference file for the full Wrapper Template.*

## 4. Impact on Safety
- **Await Deadlocks**: Calling `MyEvent.Await()` outside of a `race` block is dangerous. If `Signal()` is never called, that specific execution branch is locked forever (Memory Leak).
- **Data Races**: Because `Signal()` instantly resolves any pending `Await()`, it guarantees perfect thread-safe handoffs between concurrent blocks.

## Verification Checklist
- [ ] `event()` is used for internal async synchronization.
- [ ] `Signal()` and `Await()` are used for native events (No `Subscribe()`).
- [ ] `Await()` is wrapped in a `race` or `branch` to prevent global deadlocks.
- [ ] If `Subscribe()` is absolutely needed for an API, a custom wrapper array of callbacks is implemented.

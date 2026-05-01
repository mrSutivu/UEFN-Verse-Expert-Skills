---
name: verse-event-subscription
description: Binding to Fortnite device events (Subscribe). Managing listenable events, agents, and safe cancellation via cancelable.
---

# Event Subscription (UEFN Expert)

## Activation / When to Use
- Mandatory when reacting to standard Fortnite devices (e.g., a player enters a Mutator Zone, a player clicks a Button, an Item is picked up).
- Trigger when linking Verse logic to the physical game world.

## 1. Core Principles
Devices in UEFN expose **Events** (e.g., `InteractedWithEvent`). You use `.Subscribe()` to attach a Verse function to these events. This is the equivalent of Unreal's Dynamic Multicast Delegates.

## 2. Implementation Syntax

### The Subscription
```verse
my_device := class(creative_device):

    @editable
    MyButton : button_device = button_device{}

    OnBegin<override>()<suspends>:void=
        # Attach the callback function
        MyButton.InteractedWithEvent.Subscribe(OnButtonPressed)

    # The signature MUST match the event's payload (Usually 'agent')
    OnButtonPressed(Agent : agent):void=
        Print("Button was pressed!")
```

### The `await()` Alternative
If you don't want to create a separate callback function, and just want the code to pause until the event happens, use `.Await()`. This MUST be done in a `<suspends>` context.

```verse
OnBegin<override>()<suspends>:void=
    Print("Waiting for press...")
    
    # Execution pauses here until button is pressed
    Agent := MyButton.InteractedWithEvent.Await() 
    
    Print("Pressed! Resuming logic.")
```

## 3. Canceling Subscriptions (`cancelable`)
`Subscribe()` returns an object of type `cancelable`. If you need to stop listening to an event, you must store this object.

```verse
var ButtonSubscription : ?cancelable = false

OnBegin<override>()<suspends>:void=
    # Store the subscription
    set ButtonSubscription = option{ MyButton.InteractedWithEvent.Subscribe(OnButtonPressed) }

StopListening():void=
    # Safely unwrap and cancel
    if (Sub := ButtonSubscription?):
        Sub.Cancel()
        set ButtonSubscription = false
```

## 4. Impact on Safety
- **Memory Leaks**: If a device is destroyed or reset, hanging subscriptions can cause issues. Use `cancelable` when managing temporary listeners.
- **Await Deadlocks**: Using `.Await()` infinitely will lock that branch of execution forever. Always wrap `.Await()` inside a `race` block with a `Sleep()` timeout if the event isn't guaranteed to happen.

## Verification Checklist
- [ ] Callback function signatures perfectly match the Event's expected payload (e.g., `(Agent:agent)`).
- [ ] `.Subscribe()` is used for continuous listening.
- [ ] `.Await()` is used for one-shot, sequential logic pauses inside `<suspends>`.
- [ ] `cancelable` is stored and `.Cancel()` is called if the listener needs to be removed dynamically.

# Event Subscription Reference

## 1. Event Payload Types
Different devices pass different data. You must check the device API.

```verse
# Button passes the Agent who clicked
OnButtonPressed(Agent : agent):void=

# Trigger device passes an optional agent (might be triggered by another device)
OnTriggered(MaybeAgent : ?agent):void=
    if (Agent := MaybeAgent?):
        Print("Triggered by player")

# Mutator Zone passes the Agent entering/exiting
OnZoneEntered(Agent : agent):void=
```

## 2. Inline Await with Race (Timeout Pattern)
The safest way to use `Await()`.

```verse
WaitWithTimeout()<suspends>:void=
    race:
        # Blocks until interaction
        Agent := MyButton.InteractedWithEvent.Await()
        
        # Or timeout after 5 seconds
        block:
            Sleep(5.0)
            Print("Timeout reached.")
```

## 3. Managing Multiple Subscriptions
```verse
var Subscriptions : []cancelable = array{}

SubscribeToAll():void=
    set Subscriptions = Subscriptions + array{ ButtonA.InteractedWithEvent.Subscribe(OnA) }
    set Subscriptions = Subscriptions + array{ ButtonB.InteractedWithEvent.Subscribe(OnB) }

ClearAll():void=
    for (Sub : Subscriptions):
        Sub.Cancel()
    set Subscriptions = array{}
```

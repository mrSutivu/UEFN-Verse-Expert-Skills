# Custom Events Reference

## 1. Custom Listenable Wrapper Template
If you want to recreate the behavior of a Fortnite device's `InteractedWithEvent.Subscribe()`.

```verse
# The Wrapper Class
custom_signal := class:
    # Array holding the function pointers
    var Subscribers : []type{_(:agent) : void} = array{}

    # Method to add a function
    Subscribe(Callback : type{_(:agent) : void}) : void =
        set Subscribers = Subscribers + array{Callback}

    # Method to trigger all functions
    Invoke(Agent : agent) : void =
        for (Callback : Subscribers):
            Callback(Agent)

# Usage in a Device
my_device := class(creative_device):

    # Instantiate the custom signal
    var OnBossKilled : custom_signal = custom_signal{}

    OnBegin<override>()<suspends>:void=
        # Subscribe just like a normal device
        OnBossKilled.Subscribe(RewardPlayer)

    # Simulated Boss Death
    BossDied(Killer : agent):void=
        OnBossKilled.Invoke(Killer)

    RewardPlayer(Agent : agent):void=
        Print("Giving Gold to Player!")
```

## 2. Passing Multiple Variables via Event
If an event needs to pass more than one variable, you must use a `tuple`.

```verse
# Event expecting an int and a string
var ComplexEvent : event(tuple(int, string)) = event(tuple(int, string)){}

TriggerComplex():void=
    # Signal with a tuple
    ComplexEvent.Signal( (42, "Success") )

WaitComplex()<suspends>:void=
    # Await returns the tuple
    Result := ComplexEvent.Await()
    ID := Result(0)
    Message := Result(1)
```

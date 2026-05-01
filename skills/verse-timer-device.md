---
name: verse-timer-device
description: Using timer_device for visible UI countdowns vs using Verse Sleep() for background delays.
---

# Timer Device vs Verse Sleep (UEFN Expert)

## Activation / When to Use
- Mandatory when creating a countdown or stopwatch that the players must see on their HUD.
- Trigger when transitioning between rounds, managing bomb defusals, or racing modes.

## 1. `Sleep()` vs `timer_device`
- **`Sleep(X.0)`**: A native Verse function. Runs in the background. Invisible to the player. Highly performant.
- **`timer_device`**: A physical UEFN device. Renders a clock UI on the player's screen. Broadcasts events (`SuccessEvent`, `FailureEvent`).

## 2. Implementing the Timer Device

### Editor Setup
1. Place a `Timer Device`.
2. Set "Show on HUD" to Yes.
3. Configure the Duration.

### Verse Control
```verse
using { /Fortnite.com/Devices }

my_race_manager := class(creative_device):

    @editable RaceTimer : timer_device = timer_device{}

    OnBegin<override>()<suspends>:void=
        # Bind to outcomes
        RaceTimer.SuccessEvent.Subscribe(OnTimerCompleted)
        RaceTimer.FailureEvent.Subscribe(OnTimerFailed) # Usually if time runs out before stopped

    StartRace():void=
        # Starts the countdown for everyone
        RaceTimer.Start()

    PlayerCrossedFinishLine(Agent : agent):void=
        # Stops the timer early
        RaceTimer.Stop(Agent)
```

## 3. Background Timers (`Sleep` in `race`)
If the timer does NOT need UI (e.g., a hidden 5-second window to input a combo), always use Verse concurrency instead of a device.

```verse
    HiddenComboWindow(Agent : agent)<suspends>:void=
        race:
            # Wait for combo input
            ComboButton.InteractedWithEvent.Await()
            
            # Hidden 5 second timer
            block:
                Sleep(5.0)
                Print("Combo failed!")
```

## 4. Impact on Safety
- **Network Overhead**: The `timer_device` synchronizes its clock across the server to ensure all clients see the exact same UI milliseconds. It is relatively heavy. Do not use 50 timer devices simultaneously.
- **State Integrity**: Using `Sleep` in a `race` block guarantees that the timer is instantly destroyed and stops consuming memory if the primary action occurs.

## Verification Checklist
- [ ] `timer_device` is exclusively used for visual UI clocks.
- [ ] `.Start()`, `.Stop()`, and `.Reset()` are used to control the device.
- [ ] `Sleep()` inside a `race` block is used for all invisible background delays.

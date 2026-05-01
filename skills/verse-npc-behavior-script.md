---
name: verse-npc-behavior-script
description: Creating custom AI logic using npc_behavior. Controlling navigation, focus, and reacting to world events.
---

# NPC Behavior Scripts (UEFN Expert)

## Activation / When to Use
- Mandatory when you need custom AI that goes beyond the basic Guard Spawner settings (e.g., an NPC that patrols between specific points, heals allies, or acts as a guide).
- Trigger when you need an AI to interact with Verse Events.

## 1. The `npc_behavior` Base Class
Unlike standard devices which inherit from `creative_device`, AI scripts inherit from `npc_behavior`.

### Editor Setup
1. Create a new Verse file using the **NPC Behavior** template.
2. Create an **NPC Character Definition** asset in the Content Browser.
3. Set its Behavior type to **Verse Behavior** and select your script.
4. Assign the definition to an **NPC Spawner** device.

## 2. Implementation Syntax

### The Script Skeleton
```verse
using { /Fortnite.com/AI }
using { /Fortnite.com/Characters }
using { /Verse.org/Simulation }

my_custom_npc := class(npc_behavior):

    # Triggers when the NPC spawns
    OnBegin<override>()<suspends>:void=
        
        # Extract the necessary interfaces
        if:
            Agent := GetAgent[]
            FortChar := Agent.GetFortCharacter[]
            Navigatable := FortChar.GetNavigatable[]
            FocusInterface := FortChar.GetFocusInterface[]
        then:
            # Start AI Loop
            PatrolLoop(Navigatable, FocusInterface)

    # Triggers when the NPC dies or is despawned
    OnEnd<override>():void=
        Print("NPC Died")
```

## 3. Controlling the AI

### Navigation (`GetNavigatable`)
Moves the AI asynchronously.

```verse
    PatrolLoop(Navigatable : navigatable, FocusInterface : focus_interface)<suspends>:void=
        TargetPos := vector3{X:=1000.0, Y:=0.0, Z:=0.0}
        
        # Look at the target while moving
        FocusInterface.MaintainFocus(TargetPos)
        
        # NavigateTo is a <suspends> function. It waits until the AI reaches the target.
        NavTarget := MakeNavigationTarget(TargetPos)
        NavResult := Navigatable.NavigateTo(NavTarget, ?MovementType := movement_types.Running)
        
        if (NavResult = navigation_result.Reached):
            Print("Target Reached!")
```

## 4. Impact on Safety
- **Asynchronous Lifespan**: `OnBegin` is asynchronous. If the NPC dies, `OnBegin` is automatically cancelled. You do not need to manually check `if (IsAlive)` inside your loops.
- **Interface Failsafety**: Using the failable `if` block to get `Navigatable` ensures the script won't crash if the NPC spawned without navigation capabilities (e.g., stuck in the floor or missing a NavMesh).

## Verification Checklist
- [ ] Class inherits from `npc_behavior`.
- [ ] `GetAgent[]` and `GetFortCharacter[]` are used to access the AI's physical body.
- [ ] `GetNavigatable[]` is used for `NavigateTo` commands.
- [ ] Script is linked to an NPC Character Definition asset in UEFN.

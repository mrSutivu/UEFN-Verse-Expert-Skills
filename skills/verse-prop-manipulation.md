---
name: verse-prop-manipulation
description: Moving and rotating creative props dynamically. Utilizing MoveTo and TeleportTo for fluid or instant relocation.
---

# Prop Manipulation (UEFN Expert)

## Activation / When to Use
- Mandatory when animating objects (Doors, Elevators, Platforms) dynamically via Verse instead of Sequencer.
- Trigger when physically moving a `creative_prop`.

## 1. Core Principles
A `creative_prop` can be moved in two ways:
1. **`TeleportTo`**: Instantaneous movement. Failable expression.
2. **`MoveTo`**: Smooth, interpolated movement over time. Asynchronous (`<suspends>`).

## 2. Implementation Syntax

### Setup
Expose the prop to the editor.
```verse
@editable
MyDoor : creative_prop = creative_prop{}
```

### TeleportTo (Instant)
Because `TeleportTo` can fail (e.g., trying to teleport outside the valid game bounds), it must be in an `if`.
```verse
void InstantMove()=
    TargetPos := vector3{X := 0.0, Y := 0.0, Z := 1000.0}
    TargetRot := IdentityRotation()

    if (MyDoor.TeleportTo[TargetPos, TargetRot]):
        Print("Teleport successful.")
```

### MoveTo (Smooth)
`MoveTo` takes a Position, a Rotation, and a Time (Float). It MUST be in a `<suspends>` context.

```verse
OpenDoor()<suspends>:void=
    CurrentTransform := MyDoor.GetTransform()
    
    # Move 300 units up
    TargetPos := CurrentTransform.Translation + vector3{Z := 300.0}
    
    # Move over 2.0 seconds
    Result := MyDoor.MoveTo(TargetPos, CurrentTransform.Rotation, 2.0)

    # Check outcome
    if (Result = move_to_result.DestinationReached):
        Print("Door opened fully.")
```

## 3. Canceling and Interrupting
If a prop is currently executing a `MoveTo` over 10 seconds, and you call `MoveTo` again on the same prop before it finishes, the first movement is **instantly cancelled** and the new one begins.
The first `MoveTo` call will return a result other than `DestinationReached` (e.g., `move_to_result.WillNotReachDestination`).

## 4. Impact on Safety
- **Network Sync**: `MoveTo` handles client-server replication smoothly for the prop. You do not need to calculate the interpolation yourself every tick.
- **Asynchronous Blocking**: Because `MoveTo` pauses execution until finished, you must use `branch` or `spawn` if you want to move 5 props simultaneously (otherwise they move one by one).

## Verification Checklist
- [ ] `creative_prop` is used for the object reference.
- [ ] `TeleportTo` is wrapped in an `if` (failable).
- [ ] `MoveTo` is called from a `<suspends>` function.
- [ ] Simultaneous movements of multiple props are wrapped in `sync` or `branch` blocks to prevent sequential blocking.

# Teleporter Device Reference

## 1. Dynamic Checkpoints
Moving a single teleporter device to act as a dynamic respawn or checkpoint location.

```verse
@editable DynamicRift : teleporter_device = teleporter_device{}
@editable CheckpointTriggers : []trigger_device = array{}

OnBegin<override>()<suspends>:void=
    for (Trigger : CheckpointTriggers):
        Trigger.TriggeredEvent.Subscribe(OnCheckpointHit)

OnCheckpointHit(MaybeAgent : ?agent):void=
    # Move the Rift to the location of the trigger that was just hit
    # Note: TriggeredEvent passes an Option ?agent
    if:
        Agent := MaybeAgent?
        FortChar := Agent.GetFortCharacter[]
        # Get the location of the player at the checkpoint
        PlayerLoc := FortChar.GetTransform().Translation
    then:
        # Move the teleporter device itself!
        if (DynamicRift.TeleportTo[PlayerLoc, IdentityRotation()]):
            Print("Checkpoint updated!")
```

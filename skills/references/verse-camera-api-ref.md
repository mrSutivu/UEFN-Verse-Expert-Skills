# Camera API Reference

## 1. Dynamic Camera Swapping
Switching between multiple camera angles based on trigger zones (e.g., Resident Evil or old-school Silent Hill style).

```verse
using { /Fortnite.com/Devices }

room_camera_manager := class(creative_device):

    @editable Room1Cam : gameplay_camera_fixed_angle_device = gameplay_camera_fixed_angle_device{}
    @editable Room1Trigger : mutator_zone_device = mutator_zone_device{}

    @editable Room2Cam : gameplay_camera_fixed_angle_device = gameplay_camera_fixed_angle_device{}
    @editable Room2Trigger : mutator_zone_device = mutator_zone_device{}

    OnBegin<override>()<suspends>:void=
        Room1Trigger.AgentEntersEvent.Subscribe(OnEnterRoom1)
        Room2Trigger.AgentEntersEvent.Subscribe(OnEnterRoom2)

    OnEnterRoom1(Agent : agent):void=
        # Remove Room 2 camera if active
        Room2Cam.RemoveFrom(Agent)
        # Apply Room 1 camera
        Room1Cam.AddTo(Agent)

    OnEnterRoom2(Agent : agent):void=
        Room1Cam.RemoveFrom(Agent)
        Room2Cam.AddTo(Agent)
```

## 2. Dealing with Aiming (ADS)
If you build a Top-Down shooter with a Fixed Angle camera, the player's weapon will still try to aim where the invisible crosshair is. You often need to pair a custom camera with an `input_trigger_device` to manage custom aiming logic or use a specific Reticle device to override standard Fortnite targeting.

---
name: verse-camera-api
description: Controlling custom camera angles (Fixed Angle, Orbit) using the UEFN Camera API and Verse.
---

# Verse Camera API (UEFN Expert)

## Activation / When to Use
- Mandatory when changing the player's perspective (e.g., Top-Down, Side-Scroller, Isometric, or Custom First Person).
- Trigger when you need to override the default Fortnite third-person camera.

## 1. The Camera Devices
UEFN provides specific devices for camera manipulation. You do not code the math of the camera in Verse; you configure the device in the Editor and apply it via Verse.
- **`gameplay_camera_fixed_angle_device`**: Looks in a fixed direction regardless of player rotation (Great for 2D platformers or Isometric Diablo-likes).
- **`gameplay_camera_orbit_device`**: Orbits around the player based on mouse movement, but with restricted distances or angles.

## 2. Implementation Syntax

### Setup
```verse
using { /Fortnite.com/Devices }

my_camera_manager := class(creative_device):

    @editable
    IsometricCamera : gameplay_camera_fixed_angle_device = gameplay_camera_fixed_angle_device{}
    
    @editable
    OrbitCamera : gameplay_camera_orbit_device = gameplay_camera_orbit_device{}
```

### Applying the Camera (`AddTo`)
To hijack the player's view, you "Add" the camera to their "Camera Stack". The camera with the highest priority in the stack becomes active.

```verse
    SetCameraToIsometric(Agent : agent):void=
        # Adds the camera to the player's view
        IsometricCamera.AddTo(Agent)
        Print("Isometric view activated.")
```

### Removing the Camera (`RemoveFrom`)
When the minigame or sequence is over, remove the camera to return the player to the default Fortnite view.

```verse
    ResetCamera(Agent : agent):void=
        # Removes our custom camera, reverting to the next one in the stack (usually default)
        IsometricCamera.RemoveFrom(Agent)
        Print("Camera returned to normal.")
```

### Global Application
If an entire game mode relies on this camera (e.g., a Top-Down shooter), you can apply it to everyone.
```verse
    OnBegin<override>()<suspends>:void=
        # Applies to all current and future players automatically
        IsometricCamera.AddToAll()
```
*(Note: If `AddToAll` is used, the Editor property "Auto Add to All Players" on the device does the exact same thing without needing Verse code).*

## 3. Impact on Safety
- **Device Enablement**: Calling `AddTo(Agent)` on a Camera device that has been explicitly disabled (`Disable()`) will fail silently. Ensure the device is enabled.
- **Priority Conflicts**: If you have a Cutscene Camera (`cinematic_sequence_device`) and an Isometric Camera trying to control the view simultaneously, the one with the highest "Priority" setting in its UEFN Details panel wins.

## Verification Checklist
- [ ] `gameplay_camera_fixed_angle_device` or `orbit_device` are used instead of manual `Tick` math.
- [ ] `.AddTo(Agent)` is used to apply the camera to a specific player.
- [ ] `.RemoveFrom(Agent)` is used to restore the default Fortnite view.
- [ ] Device is enabled before calling `AddTo`.

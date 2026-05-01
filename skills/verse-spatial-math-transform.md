---
name: verse-spatial-math-transform
description: Understanding SpatialMath in Verse. Handling vector3, rotation, and transforms for manipulating objects in 3D space.
---

# Verse SpatialMath & Transforms (UEFN Expert)

## Activation / When to Use
- Mandatory when calculating distances, creating offsets, or determining orientations in 3D space.
- Trigger when working with locations or movements of players, props, or devices.

## 1. The Core Module
To use 3D math in Verse, you must include the `SpatialMath` module.

```verse
using { /UnrealEngine.com/Temporary/SpatialMath }
```

## 2. Core Data Types

### `vector3`
Represents a location, direction, or scale in 3D space.
- **Initialization**: `vector3{X := 100.0, Y := 50.0, Z := 0.0}`
- **Constants**: `ZeroVector`, `UpVector`, `ForwardVector` are available but usually constructed manually.

### `rotation`
Represents an orientation (internally a Quaternion to avoid Gimbal Lock).
- **Initialization**: You rarely construct rotations manually via Quaternions. Use helpers.
- **Helpers**: 
  - `MakeRotation(Axis: vector3, AngleRadians: float)`
  - `IdentityRotation()`

### `transform`
A struct containing Translation (`vector3`), Rotation (`rotation`), and Scale (`vector3`).
- **Initialization**: `transform{Translation := vector3{...}, Rotation := IdentityRotation(), Scale := vector3{X:=1.0, Y:=1.0, Z:=1.0}}`

## 3. Manipulating Rotations
Because rotations are Quaternions, you modify them using mathematical functions, not by adding Euler angles.

```verse
CurrentRot := MyProp.GetTransform().Rotation

# Add 90 degrees on the Z-axis (Yaw)
NewRot := CurrentRot.ApplyYaw(DegreesToRadians(90.0))

# Or apply pitch/roll
NewRotPitch := CurrentRot.ApplyPitch(3.14159 / 2.0) # 90 deg in radians
```
*Note: Depending on the specific UE/Verse version, `ApplyYaw` might take degrees or radians. Check current API docs. Usually, explicit conversion `DegreesToRadians()` is safer.*

## 4. Impact on Safety
- **Immutability**: `vector3` and `rotation` are values. You cannot do `MyVector.X = 10.0`. You must construct a new vector or use `set MyVector = vector3{X := 10.0, Y := MyVector.Y, Z := MyVector.Z}`.
- **Gimbal Lock**: By forcing the use of `rotation` (Quaternions) and helper functions rather than raw Euler angles (`X, Y, Z` rotations), Verse prevents massive mathematical bugs when objects rotate past 90 degrees.

## Verification Checklist
- [ ] `SpatialMath` module is imported.
- [ ] `vector3` is used for positional data.
- [ ] `rotation` is manipulated via helper functions (like `ApplyYaw`) rather than raw math.
- [ ] Variables are reassigned completely when modifying a `vector3` component.

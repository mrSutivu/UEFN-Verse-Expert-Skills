# SpatialMath Reference

## 1. Distance Calculation
Calculating the distance between two points.

```verse
using { /UnrealEngine.com/Temporary/SpatialMath }

IsCloseEnough(PosA : vector3, PosB : vector3):logic=
    # Distance returns a float
    Dist := Distance(PosA, PosB)
    return Dist < 500.0
```

## 2. Forward Vector (Direction)
Getting the direction an object is facing.

```verse
GetForward(Transform : transform):vector3=
    # Extracts the forward direction vector from a rotation
    Rot := Transform.Rotation
    return Rot.GetLocalForward()
```

## 3. Applying an Offset Relative to Rotation
If you want to spawn an item exactly 200 units *in front* of a player.

```verse
CalculateSpawnLocation(PlayerTransform : transform):vector3=
    # 1. Get Forward Direction
    ForwardDir := PlayerTransform.Rotation.GetLocalForward()
    
    # 2. Multiply direction by distance to get the offset vector
    Offset := ForwardDir * 200.0
    
    # 3. Add offset to original position
    return PlayerTransform.Translation + Offset
```

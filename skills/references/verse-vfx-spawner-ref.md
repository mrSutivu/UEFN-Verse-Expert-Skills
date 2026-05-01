# VFX Spawner Reference

## 1. Simple Object Pooling for VFX
If you need to play the same effect rapidly at different locations (e.g., Bullet Impacts), you need a pool of props+spawners.

```verse
@editable ImpactProps : []creative_prop = array{}
@editable ImpactVFXs : []vfx_spawner_device = array{}

var CurrentVFXIndex : int = 0

PlayImpactAt(HitLocation : vector3):void=
    # Get current items from pool
    if:
        Prop := ImpactProps[CurrentVFXIndex]
        VFX := ImpactVFXs[CurrentVFXIndex]
    then:
        # Move and Fire
        if (Prop.TeleportTo[HitLocation, IdentityRotation()]):
            VFX.Begin()
            
        # Increment and loop index
        set CurrentVFXIndex += 1
        if (CurrentVFXIndex >= ImpactProps.Length):
            set CurrentVFXIndex = 0
```

## 2. Toggling Effects with `defer`
Ensuring a continuous effect turns off even if the logic is interrupted.

```verse
ShowLaserBeamForDuration(Duration : float)<suspends>:void=
    # Turn on laser
    LaserVFX.Enable()
    
    defer:
        # Guarantee it turns off
        LaserVFX.Disable()
        
    Sleep(Duration)
```

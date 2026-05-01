# Spawning Reference

## 1. Spawning and Moving (Projectile Logic)
Combining `SpawnProp` and `MoveTo` for a custom projectile.

```verse
FireProjectile(SpawnLoc : vector3, TargetLoc : vector3)<suspends>:void=
    # 1. Spawn
    Result := SpawnProp(BulletAsset, SpawnLoc, IdentityRotation())
    
    if (Bullet := Result(1)?):
        # 2. Move
        MoveRes := Bullet.MoveTo(TargetLoc, IdentityRotation(), 1.0)
        
        # 3. Handle Impact
        if (MoveRes = move_to_result.DestinationReached):
            Print("Hit!")
            # Trigger explosion VFX here
            
        # 4. Cleanup
        Bullet.Dispose()
```

## 2. Managing Spawned AI
Listening to AI spawner events.

```verse
OnBegin<override>()<suspends>:void=
    GuardSpawner.SpawnedEvent.Subscribe(OnGuardSpawned)
    GuardSpawner.EliminatedEvent.Subscribe(OnGuardKilled)

OnGuardSpawned(GuardAgent : agent):void=
    Print("A new guard entered the arena!")

OnGuardKilled(Result : device_ai_interaction_result):void=
    Print("A guard was defeated.")
```

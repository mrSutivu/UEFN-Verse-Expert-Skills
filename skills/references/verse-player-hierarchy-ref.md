# Player Hierarchy Reference

## 1. Healing and Damaging a Character
Using the `Damage()` and `Heal()` methods correctly.

```verse
using { /Fortnite.com/Characters }
using { /Fortnite.com/Game } # Needed for damage args

ApplyCombatEffects(InAgent : agent):void=
    if (FortChar := InAgent.GetFortCharacter[]):
        
        # Applying Damage (Can kill the player)
        DamageArgs := damage_args:
            Amount := 50.0
            Instigator := option{InAgent} # Who caused it
            Source := option{InAgent} # The weapon/device

        FortChar.Damage(DamageArgs)
        
        # Healing
        HealArgs := heal_args:
            Amount := 25.0
            
        FortChar.Heal(HealArgs)
```

## 2. Teleporting a Character
Moving the physical avatar in the world.

```verse
using { /UnrealEngine.com/Temporary/SpatialMath }

TeleportPlayer(InAgent : agent, Destination : vector3):void=
    if (FortChar := InAgent.GetFortCharacter[]):
        
        # TeleportTo is failable if the location is completely invalid
        if (FortChar.TeleportTo[Destination, rotation{}]):
            Print("Teleported!")
```

## 3. Getting All Players
Fetching the global array of human players from the `playspace`.

```verse
using { /Fortnite.com/Playspaces }

GetAllHumanPlayers():[]player=
    return GetPlayspace().GetPlayers()
```

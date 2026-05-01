# Character Events Reference

## 1. Lifesteal Mechanic (Vampirism)
Using `DamagedEvent` to heal the attacker.

```verse
using { /Fortnite.com/Characters }
using { /Fortnite.com/Game }

OnPlayerDamaged(Result : damage_result):void=
    DamageTaken := Result.Amount
    
    # 1. Find who caused the damage
    if:
        AttackerAgent := Result.Instigator?
        AttackerChar := AttackerAgent.GetFortCharacter[]
    then:
        # 2. Calculate 50% Lifesteal
        HealAmount := DamageTaken * 0.5
        
        # 3. Heal the attacker
        AttackerChar.Heal(heal_args{ Amount := HealAmount })
        Print("Lifesteal applied: {HealAmount}")
```

## 2. Safe Event Subscription (Tracking Connect/Disconnect)
How to safely bind and unbind character events.

```verse
var PlayerDeathSubs : weak_map(player, cancelable) = map{}

OnBegin<override>()<suspends>:void=
    Playspace := GetPlayspace()
    Playspace.PlayerAddedEvent().Subscribe(OnPlayerJoined)
    Playspace.PlayerRemovedEvent().Subscribe(OnPlayerLeft)

OnPlayerJoined(Player : player):void=
    if (FortChar := Player.GetFortCharacter[]):
        # Store the subscription
        Sub := FortChar.EliminatedEvent().Subscribe(OnPlayerDied)
        if (set PlayerDeathSubs[Player] = Sub) {}

OnPlayerLeft(Player : player):void=
    # Safely Cancel to free memory
    if (Sub := PlayerDeathSubs[Player]):
        Sub.Cancel()
        
OnPlayerDied(Result : elimination_result):void=
    Print("Someone died.")
```

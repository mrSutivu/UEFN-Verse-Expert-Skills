# Tracker Device Reference

## 1. Custom Stat Tracking
Linking a Verse event to a Tracker.

```verse
@editable CoinTracker : tracker_device = tracker_device{}
@editable CoinTriggers : []trigger_device = array{}

OnBegin<override>()<suspends>:void=
    for (Coin : CoinTriggers):
        Coin.TriggeredEvent.Subscribe(OnCoinCollected)

OnCoinCollected(MaybeAgent : ?agent):void=
    if (Agent := MaybeAgent?):
        # Update the UI tracker
        CoinTracker.Increment(Agent)
```

## 2. Multi-Step Quests
Chaining trackers together. When Tracker 1 finishes, assign Tracker 2.

```verse
@editable QuestStep1 : tracker_device = tracker_device{}
@editable QuestStep2 : tracker_device = tracker_device{}

OnBegin<override>()<suspends>:void=
    QuestStep1.CompleteEvent.Subscribe(OnStep1Done)
    QuestStep2.CompleteEvent.Subscribe(OnStep2Done)

OnStep1Done(Agent : agent):void=
    # Remove old UI
    QuestStep1.Remove(Agent)
    # Give new objective
    QuestStep2.Assign(Agent)

OnStep2Done(Agent : agent):void=
    Print("Full Questline complete!")
```

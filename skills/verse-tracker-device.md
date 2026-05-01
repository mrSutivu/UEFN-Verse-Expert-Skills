---
name: verse-tracker-device
description: Managing player progress, quests, and custom UI stats using the tracker_device.
---

# Tracker Device & Quests (UEFN Expert)

## Activation / When to Use
- Mandatory when building quest systems, tracking kills/collectables, or displaying custom stat bars on the HUD.
- Trigger when you need persistent or session-based objective tracking without building custom Verse UI Canvas.

## 1. The `tracker_device`
The Tracker is the ultimate workhorse for objectives. It handles the counting, the UI (progress bar), and completion events.

### Editor Setup
1. Place a `Tracker Device`.
2. Set "Stat to Track" (e.g., Custom, Eliminations).
3. Set "Target Value" (e.g., 10).
4. Enable "Show on HUD".

## 2. Implementation Syntax

### Linking in Verse
```verse
using { /Fortnite.com/Devices }

my_quest_manager := class(creative_device):

    @editable QuestTracker : tracker_device = tracker_device{}

    OnBegin<override>()<suspends>:void=
        # Listen for quest completion
        QuestTracker.CompleteEvent.Subscribe(OnQuestCompleted)

    OnQuestCompleted(Agent : agent):void=
        Print("Player finished the quest!")
        # Grant reward
```

### Modifying Progress
You can increment or set the tracker's value via Verse.

```verse
    AddProgress(Agent : agent):void=
        # Add 1 to the current count for this specific player
        QuestTracker.Increment(Agent)

    ForceComplete(Agent : agent):void=
        # Instantly fills the bar and triggers CompleteEvent
        QuestTracker.Complete(Agent)
```

## 3. Assigning vs Global
Trackers can be Global (shared by the whole team/server) or Individual.
If Individual, you must assign the tracker to the player before it shows up on their screen.

```verse
    StartQuestForPlayer(Agent : agent):void=
        # Gives the tracker to the player, showing the UI
        QuestTracker.Assign(Agent)
        
    RemoveQuest(Agent : agent):void=
        # Removes the UI and progress
        QuestTracker.Remove(Agent)
```

## 4. Impact on Safety
- **UI Simplification**: Using a Tracker for a "Collect 10 Coins" objective is infinitely safer and more performant than building a custom Canvas UI and manually updating a text block 10 times.
- **Built-in Persistence**: Trackers can be configured in the Editor to automatically save their progress using the native Fortnite persistence system, avoiding the need for complex `weak_map` code for simple quests.

## Verification Checklist
- [ ] `tracker_device` is used for objective counting and HUD progress bars.
- [ ] `.Assign(Agent)` is called if the tracker is not set to auto-assign globally.
- [ ] `.Increment(Agent)` or `.Complete(Agent)` are used to update progress.
- [ ] `.CompleteEvent.Subscribe()` is used to trigger reward logic.

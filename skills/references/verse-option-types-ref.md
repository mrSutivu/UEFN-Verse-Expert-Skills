# Verse Option Types Reference

## 1. Option Return Types in Functions
How to write a function that might not find a result.

```verse
FindHighestScore(Scores : []int)<decides>:int=
    if (Scores.Length > 0):
        # Logic here
        return Scores[0]
    else:
        # Fails automatically by not hitting a return
        false? # Forces failure
```
*Note: In Verse, if a `<decides>` function doesn't reach a valid return, it fails. You can also explicitly fail using `false?`.*

## 2. Default Values with `or`
Unwrapping an option inline providing a default if it's `false`.

```verse
var PlayerScore : ?int = false

# If PlayerScore is false, use 0.
ActualScore := PlayerScore? or 0
```

## 3. Options of Classes
When holding references to players or custom classes.

```verse
var CurrentTarget : ?agent = false

if (Target := CurrentTarget?):
    # Do something with the agent
```

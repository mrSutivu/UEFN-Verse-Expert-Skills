# Verse Collections Reference

## 1. Array Iteration
Filtering and iterating arrays.

```verse
var Numbers : []int = array{1, 2, 3, 4, 5, 6}

# Simple loop
for (Num : Numbers):
    Print("{Num}")

# Loop with failable filter (Only even numbers)
for:
    Num : Numbers
    Mod[Num, 2] = 0 # Failable expression
do:
    Print("Even: {Num}")
```

## 2. Map Iteration
Iterating over Keys and Values simultaneously.

```verse
PlayerScores : [agent]int = map{PlayerA => 10, PlayerB => 20}

# Iterate both
for (Player -> Score : PlayerScores):
    Print("Score is {Score}")
```

## 3. Removing from an Array
Verse doesn't have a built-in `Remove()` yet. You must build a new array without the target element.

```verse
RemoveFromArray(Target : int, OldArray : []int):[]int=
    var NewArray : []int = array{}
    for (Num : OldArray, Num <> Target):
        set NewArray = NewArray + array{Num}
    return NewArray
```

## 4. Tuples
Tuples group fixed, unnamed data.
```verse
# Initialization
MyTuple : tuple(int, string) = (42, "Apples")

# Accessing (0-indexed)
Count := MyTuple(0)
Name := MyTuple(1)
```

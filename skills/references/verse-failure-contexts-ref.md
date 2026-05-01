# Verse Failure Contexts Reference

## 1. Failable Array Access and Assignment
In Verse, you use `[]` for failable access and `:=` for constant assignment.

```verse
# Example: Getting the first player from the playspace
Players : []player = GetPlayspace().GetPlayers()

# Try to get index 0. If it fails, the 'if' fails.
if (FirstPlayer := Players[0]):
    Print("Got the first player!")
```

## 2. Advanced `for` Loop Filtering
Combining assignment and failure in a single line.

```verse
# Assume GetHealth() returns an option ?float
for:
    Player : Players
    Health := Player.GetHealth() # Unwraps option, fails if missing
    Health > 50.0                # Fails if health is low
do:
    Print("Player is healthy enough!")
```

## 3. The `or` Operator (Fallback)
If the left side fails, try the right side.

```verse
# Try to get index 5, if it fails, use "Default"
MyString := StringArray[5] or "Default"
```

## 4. Custom Failable Functions
```verse
MakeTransaction(Cost:int, Balance:int)<decides><transacts>:int=
    # 1. Check condition (fails if true)
    Cost <= Balance
    
    # 2. Modify state
    NewBalance := Balance - Cost
    
    # 3. Return value on success
    return NewBalance
```

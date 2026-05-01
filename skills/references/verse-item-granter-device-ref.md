# Item Granter Reference

## 1. Class / Loadout Selection
Using an array of granters to let players pick a class.

```verse
@editable ClassGranters : []item_granter_device = array{}
@editable ClassButtons : []button_device = array{}

OnBegin<override>()<suspends>:void=
    # Assuming indices match (Button 0 triggers Granter 0)
    for (Index -> Button : ClassButtons):
        # We must capture the index using a helper function 
        # to avoid the loop closure overwriting the variable
        spawn{ BindButton(Button, Index) }

BindButton(Button : button_device, Index : int)<suspends>:void=
    loop:
        Agent := Button.InteractedWithEvent.Await()
        
        # Failable array access
        if (Granter := ClassGranters[Index]):
            Granter.GrantItem(Agent)
            Print("Class Granted!")
```

*Note: In Verse, if you subscribe to an event inside a `for` loop and rely on the loop's iterator variable (like `Index`), you often need to wrap it in a separate function to capture the variable's value safely at that specific iteration.*

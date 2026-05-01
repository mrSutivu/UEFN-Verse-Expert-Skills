# Verse UI Reference

## 1. Complex Canvas Layout (Stack Box)
For organizing multiple elements in a row or column, use `stack_box`.

```verse
CreateMenu():canvas=
    # Create Widgets
    Title := text_block{ DefaultText := set_text("Main Menu") }
    BtnPlay := button_loud{ DefaultText := set_text("Play") }
    BtnExit := button_quiet{ DefaultText := set_text("Exit") }

    # Group them in a vertical stack
    MyStack := stack_box:
        Orientation := orientation.Vertical
        Slots := array:
            stack_box_slot{ Widget := Title }
            stack_box_slot{ Widget := BtnPlay, Padding := margin{Top := 20.0} }
            stack_box_slot{ Widget := BtnExit, Padding := margin{Top := 10.0} }

    # Put the stack in a canvas to center it
    MyCanvas := canvas:
        Slots := array:
            canvas_slot:
                Anchors := anchors{ Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5} }
                Alignment := vector2{X:=0.5, Y:=0.5}
                Widget := MyStack
                
    return MyCanvas
```

## 2. Dynamic Text Updates
Updating a `text_block` at runtime.

```verse
var ScoreText : text_block = text_block{ DefaultText := set_text("Score: 0") }

# Method to format text dynamically
StringToMessage<localizes>(StringVal : string) : message = "{StringVal}"

UpdateScore(NewScore : int):void=
    # Convert int to string, then to message, then apply
    ScoreMsg := StringToMessage("Score: {NewScore}")
    ScoreText.SetText(ScoreMsg)
```

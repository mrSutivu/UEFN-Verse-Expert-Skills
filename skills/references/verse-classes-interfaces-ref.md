# Classes & Interfaces Reference

## 1. Class Inheritance
Subclassing to share behavior.

```verse
animal := class:
    var Name : string = "Unknown"
    MakeSound():void=
        Print("...")

dog := class(animal):
    # Override the parent method
    MakeSound<override>():void=
        Print("Woof!")
```

## 2. Abstract Classes
If a class provides a template but shouldn't be instantiated directly.
*Note: In Verse, if a class has a method without a body, it acts as an abstract interface/class.*

```verse
shape := class:
    # Method without body
    GetArea():float

circle := class(shape):
    Radius : float = 5.0
    GetArea<override>():float=
        return 3.14159 * Radius * Radius
```

## 3. The `unique` Specifier
If you need instances of a class to be comparable by identity (Instance A == Instance B), mark it `<unique>`. By default, Verse compares classes by value (structural equality).

```verse
# Identity equality (Reference comparison)
my_manager := class<unique>:
    var State : int = 0
```

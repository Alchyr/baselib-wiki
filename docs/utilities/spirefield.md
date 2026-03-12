---
title: SpireField
parent: Utilities
---

SpireField is a simple way for mods to effectively add a new field to a preexisting class in the game.

To use it, define a SpireField like so:

`public static readonly SpireField<TargetType, FieldType> MyField = new(()=>default value);`

This field can be placed in any class; you are recommended to place it somewhere related to the field's purpose.

Getting and setting the field can be done like so, given the following example:

```c#
public class SpecialNumberThing {
    public static readonly SpireField<CardModel, int> SpecialNumber = new(() => 0); //Default value of 0
}

...
CardModel model = ModelDb.Card<Bash>();
int val = SpecialNumberThing.SpecialNumber.Get(model);

SpecialNumberThing.SpecialNumber.Set(model, 5);
```

You can also get or set the value using an indexer.

```c#
CardModel model = ModelDb.Card<Bash>();
int val = SpecialNumberThing.SpecialNumber[model];

SpecialNumberThing.SpecialNumber[model] = 5;
```

### Technical

SpireField works by wrapping a ConditionalWeakTable. A ConditionalWeakTable can be used directly for similar results, but SpireField provides some utility to make it simpler to use, such as having a default value.
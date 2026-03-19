---
title: Custom Enums
parent: Utilities
---

By marking a static field of an enum type with the `[CustomEnum]` attribute, a new value will be assigned to it at runtime.

```c#
    [CustomEnum]
    public static CardKeyword Keyword;
```

It should be a public static field. A readonly field may fail to be set properly.

`CustomEnum` has additional functionality for a few specific enum types.

## Keywords

It is important to note that the `CardKeyword` enum in Slay the Spire 2 is not the same as Slay the Spire 1's keywords, which encompassed all highlighted text that would generate a tooltip. In Slay the Spire 2, `CardKeyword` is exclusively for single words that affect card behavior and don't have an associated number. So `Sly` is a `CardKeyword`, but `Summon` is not. If you need a word with an associated number, see [Dynamic Variable Tooltips]({% link docs/utilities/var-loc.md %}) instead.

After defining a `CardKeyword` enum value, you will need to set up localization for it. It will require a `title` and `description` entry with the ID being `MODPREFIX-KEYWORD_NAME`.

`CardKeywords` can be automatically added to card text. Basegame does this for all of its `CardKeywords`, meaning card localization does not say `Exhaust` or `Sly`, as it is added automatically to cards with this keyword. To do this for your custom keyword, add the `KeywordProperties` attribute after the `CustomEnum` attribute, like so.

```c#
    [CustomEnum, KeywordProperties(AutoKeywordPosition.Before)]
    public static CardKeyword Keyword;
```

If you want the keyword's ID to be different from the variable name, you can pass a name to the `CustomEnum` attribute.

## Card Piles

Adding a card pile is not fully tested and may still have some issues, which is why it does not currently have a dedicated page.

To create a custom card pile, first create a class inheriting [`CustomPile`](https://github.com/Alchyr/BaseLib-StS2/blob/master/Abstracts/CustomPile.cs).

Inside the class, define a `CustomEnum` for the `CardPile` enum and pass it to the base constructor.

Overriding/implementing the methods defined in `CustomPile` will create a functional pile that you can use in `CardPileCmd` with the defined enum to move cards to and from it. However, the visual aspects are not handled by `CustomPile` other than the animation for cards transitioning to and from your pile's position (assuming the pile does not keep cards visible, and doesn't need a special transition animation).

You can get the current instance of your custom pile in combat with `CustomPiles.GetCustomPile(combatState, pileType)`.
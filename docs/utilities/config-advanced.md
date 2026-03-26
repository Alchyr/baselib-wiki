---
title: Advanced
parent: Mod Configuration
---

# Advanced mod configuration

The content on the parent page is intended to be enough for almost every mod, but you can also build a UI using the building blocks used to implement the automatic UI, or even go bare metal and add plain Godot controls.

If you override `SetupConfigUI` in your `SimpleModConfig` subclass and don't call the base method, you get all the data persistence, with none of the default UI.  
You can then build the UI automatically and append (or prepend)  additional content, build a UI manually with helpers, or build it from scratch.

```csharp
public override void SetupConfigUI(Control optionContainer)
{
    // Insert whatever into optionContainer, and it will be shown in your config UI
}
```

## Load/save

If you change your properties at runtime (i.e. outside the configuration menu), or need to reload them from disk, you can use:  
`ModConfig.SaveDebounced<MyModConfig>()`  
`ModConfig.Load<MyModConfig>()`  

SaveDebounced uses a 1000 ms debounce delay by default, i.e. if it is called again within 1000 ms, the old request is canceled, and the new replaces it.

## Buttons

Methods (static or instance) marked with `[ConfigButton("ButtonLabel")]` generate buttons in the UI.

**Source code order is preserved** for properties as well as methods, so if you want to place a button between two properties, simply 
define the `[ConfigButton]` method between said properties.

```csharp
// "HelloWorld" is used for the text on the button, while the method name "ExampleButton" is used
// for the row label; both are localized as properties are.
[ConfigButton("HelloWorld")] 
public static void ExampleButton(BaseLibConfig myConfigInstance, NConfigButton clickedButton,
                                 NConfigOptionRow row)
{
    // All parameters are optional, and can be specified in any order with any name.
    // None of them are needed for this example, but they are kept to show the supported types.
    
    MainFile.Logger.Info("Hello, world!");

    // We can update settings here, too
    // ExplosionSize = 100;
    // ExplosionRate = 0.75;
}

[ConfigButton("ClickMe")]
public void NonStaticButton() 
{
    MainFile.Logger.Info($"Hello from instance {this}");
}
```

<img src="{{ '/images/config-button.jpg' | relative_url }}" alt="Example button" />

## Customize controls

You can fetch a control after it has been generated, and modify it.

For example, say your mod has a pool of custom cards, and the player drafts a deck at the start of a run.  
You could add a config option to let the player set the number of custom cards to pick, but the value can't be higher than the number of cards they have access to (assuming no duplicates).

Attributes like `[SliderRange]` only support values known at compile time, so you need to do this manually.

```csharp
public static double DraftPoolSize { get; set; } = 10;

public override void SetupConfigUI(Control optionContainer)
{
    // Currently, this only does two things:
    // GenerateOptionsForAllProperties(optionContainer); 
    // AddRestoreDefaultsButton(optionContainer);
    base.SetupConfigUI(optionContainer);

    int totalCustomCardsLoaded = YourMod.GetAllCustomCards().Count;

    var sliderRow = optionContainer.GetNode<NConfigOptionRow>(nameof(DraftPoolSize));
    if (sliderRow?.SettingControl is NConfigSlider slider)
    {
        // Update the slider limits
        slider.SetRange(1, totalCustomCardsLoaded, 1);
        
        // Make the slider 500 px wide (default is 324 px)
        slider.CustomMinimumSize = new Vector2(500, slider.CustomMinimumSize.Y);
    }
}
```
<img src="{{ '/images/config-wide-slider.jpg' | relative_url }}" alt="Extra wide slider" />


## Additional methods

`SimpleModConfig` has `GenerateOptionsForAllProperties` and `AddRestoreDefaultsButton` which together make up the entirety of the automatic UI generation.  
`NConfigSlider` has a `SetRange` method, to update the range at runtime.  
`NConfigButton` has a `SetColor` method that takes HSV parameters to change its color. (The game uses a HSV shader to change the color.)  
`SimpleModConfig` has `Create*Option`, `CreateSectionHeader` and `CreateDividerControl` methods to create a single option row, including layout. Most of the time, using auto-generation and customization will be easier, see above.  
`ModConfig` has `CreateRaw*Control` options that create a control with no layout, no label, no hover tip, etc.
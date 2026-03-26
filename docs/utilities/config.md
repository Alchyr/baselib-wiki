---
title: Mod Configuration
parent: Utilities
---

Mods that want customizable config options can register a config with BaseLib.

## Setup

First, the config options must be defined. Create a class inheriting from `SimpleModConfig`.
```c#
internal class MyModConfig : SimpleModConfig {

}
```

Inside this class, define a **static** property for each config value you need. The value assigned to it will be its default value.

```c#
internal class MyModConfig : SimpleModConfig {
    public static bool RandomExplosions { get; set; } = true;
    public static double ExplosionSize { get; set; } = 5000;
}
```

Lastly, register your configuration. This is best done in your mod's initializer, preferably at the start of it so that config values are loaded and set before the rest of your code runs.

```c#
public static void Initialize() {
    ModConfigRegistry.Register(ModId, new MyModConfig());
    ...
}
```

This is all you need to do in order to have BaseLib load and save your configuration data automatically, as well as create a configuration UI.  

After doing this, you should be able to find your mod's config screen by going to Settings -> Mod Settings -> Your Mod -> Settings button.  
Creating a separate screen to avoid going through this many layers is under consideration.

## Localization

The names of properties displayed on the configuration screen can be changed by adding an entry to `settings_ui.json` in your mod's localization. For each property, its localization key will be `MODNAME-PROPERTY_NAME.title`. The title of the configuration screen can be set with `MODNAME.mod_title`. If this example mod config was in the mod `RandomExplosions` this is what its localization might look like:

```json
{
  "RANDOMEXPLOSIONS.mod_title": "Random Explosions",
  "RANDOMEXPLOSIONS-RANDOM_EXPLOSIONS.title": "Enable Random Explosions",
  "RANDOMEXPLOSIONS-EXPLOSION_SIZE.title": "Explosion Size"
}
```

See the [Setup page for the mod template](https://github.com/Alchyr/ModTemplate-StS2/wiki/Setup) for information about localization and how to get the above into the game.

## Automatic UI
The following types are currently supported:

| Type     | Generates                                                             |
|----------|-----------------------------------------------------------------------|
| bool     | Checkbox                                                              |
| double   | Slider                                                                |
| string   | LineEdit                                                              |
| Any enum | Dropdown                                                              |
| Method   | Button (see [Advanced]({% link docs/utilities/config-advanced.md %})) |

Static properties of other types need to be marked with `[ConfigIgnore]`.

## Config Attributes

Attributes can be added to your `SimpleModConfig` for additional customization.

```c#

// Enables hover tips for all properties unless the individual property has them disabled
// by marking them with [ConfigHoverTip(false)]
[HoverTipsByDefault]
internal class MyModConfig : SimpleModConfig {
    // Adds a hover tip for just this property, uses .hover.desc and .hover.title (optional) suffixes
    // in localization. Not necessary with [HoverTipsByDefault] on the class.
    [ConfigHoverTip]
    public static bool RandomExplosions { get; set; } = true;

    // Adds a section separator with a title. Can be localized the same way as properties.
    [ConfigSection("ExplosionSettings")]
    // Sets the min, max, and step of a double.
    [SliderRange(0, 1, 0.01)]
    public static double ExplosionRate { get; set; } = 0.1;

    // See https://learn.microsoft.com/en-us/dotnet/standard/base-types/custom-numeric-format-strings
    // for formatting details
    [SliderLabelFormat("{0:#,#}x")]
    public static double ExplosionSize { get; set; } = 5000;

    // Allows international letters, numbers, spaces, dashes
    [ConfigTextInput(TextInputPreset.SafeDisplayName, MaxLength = 12)]
    public static string ExplosionShout { get; set; } = "KABOOM";

    // Allows exactly 6 hex characters (A-F, 0-9)
    // MaxLength makes it impossible to enter more than 6 characters, while the regex ensures
    // that the value is reverted if the user leaves the LineEdit with an invalid value
    [ConfigTextInput("[A-Fa-f0-9]{6}", MaxLength = 6)]
    public static string ExplosionColor { get; set; } = "FF0000";

    // Will be loaded and saved, but not shown in the UI.
    // If you change the value at runtime, call ModConfig.SaveDebounced<MyModConfig>() to ensure that
    // the new value is written to disk.
    [ConfigHideInUI]
    public static int TotalExplosionCount { get; set; } = 0;

    // Won't be loaded, saved or shown in the UI
    [ConfigIgnore]
    public static int UnrelatedStaticProperty { get; set; } = 0;
}
```

With all localization strings added, the config above looks like this (v0.2.1):

<img src="{{ '/images/config-standard.jpg' | relative_url }}" width="736" alt="Generated Config UI" />
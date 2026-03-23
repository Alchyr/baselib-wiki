---
title: Mod Configuration
parent: Utilities
---

Mods that want customizable config options can register a config with BaseLib.

First, the config options must be defined. Create a class inheriting `ModConfig` or `SimpleModConfig`. If using `ModConfig` you will need to set up the UI for it yourself.

```c#
internal class MyModConfig : SimpleModConfig {

}
```

Inside this class, define a static property for each config value you need. The value assigned to it will be its default value.

```c#
internal class MyModConfig : SimpleModConfig {
    public static bool RandomExplosions { get; set; } = true;
    public static double ExplosionSize { get; set; } = 5000;
}
```

Currently `bool`, `double`, and enum values are supported by `SimpleModConfig`.

Lastly, register your configuration. This is best done in your mod's initializer, preferably at the start of it so that config values are initialized before the rest of your code runs.

```c#
public static void Initialize() {
    ModConfigRegistry.Register(ModId, new MyModConfig());
```

After doing this, you should be able to find your mod's config screen by going to Settings -> Mod Settings -> Your Mod -> Settings button.

Creating a separate screen to avoid going through this many layers is under consideration.

## Localization

The names of properties displayed on the configuration screen can be changed by adding an entry to `settings_ui.json` in your mod's localization. For each property, its localization key will be `MODNAME-PROPERTY_NAME.title`. The title of the configuration screen can be set with `MODNAME.mod_title`. If this example mod config was in the mod `RandomExplosions` this is what its localization might look like.

```json
{
  "RANDOMEXPLOSIONS.mod_title": "Random Explosions",
  "RANDOMEXPLOSIONS-RANDOM_EXPLOSIONS.title": "Enable Random Explosions",
  "RANDOMEXPLOSIONS-EXPLOSION_SIZE.title": "Explosion Size"
}
```

## Config Attributes

Attributes can be added to your `SimpleModConfig` for additional customization.

```c#
//[HoverTipsByDefault] enables hover tips for all attributes unless that individual attribute has them disabled with [ConfigHoverTip(false)]
internal class MyModConfig : SimpleModConfig {
    [ConfigHoverTip] //Adds a hover tip, uses .hover.desc and .hover.title (optional) entries in localization.
    public static bool RandomExplosions { get; set; } = true;
    [ConfigSection("ExplosionSettings")] //Adds a section separator with a title. Can be localized the same way as properties.
    [SliderRange(0, 1, 0.01)] //Sets the min, max, and step of a double.
    public static double ExplosionRate { get; set; } = 0.1;
    [SliderLabelFormat("{0:#,#}x")] //See https://learn.microsoft.com/en-us/dotnet/standard/base-types/custom-numeric-format-strings for formatting details
    public static double ExplosionSize { get; set; } = 5000;
}
```
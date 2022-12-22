<!-- TODO: Add Steam Workshop badge. -->
<!--<a href="https://steamcommunity.com/sharedfiles/filedetails/?id=2653885896"><img src="https://img.shields.io/endpoint.svg?url=https%3A%2F%2Fshieldsio-steam-workshop.jross.me%2F2653885896&style=for-the-badge" alt="Steam Workshop subscribers"></a>-->

Snowfall Visual Effect
======================
_(a Crusader Kings III mod)_

This repository contains CK3 mod files which implement shader-based seasonal snowfall effect on the map, originally made for [**Godherja: The Dying World**](https://steamcommunity.com/sharedfiles/filedetails/?id=2326030123), tied to the winter severity system provided by vanilla CK3.
<a name="demo"></a>

![Animated GIF with rotating camera showing snow falling on a small part of CK3 terrain](https://media.githubusercontent.com/media/terrapass/ck3-mod-snowfall/master/promo/ck3_snowfall_0.gif)

**The code is up-to-date as of CK3 v1.8.1 (Robe).**

The contents of `mod/` directory in this repo are at the same time a small working mod in their own right, as well as a set of files ready for integration into any larger mods or total conversions, interested in having more atmospheric (pun intended) visual effects on the map. See [Integrating into Your Mod](#integration) for details.

Table of Contents
-----------------
1. <a href="#description">Mod Description</a>
2. <a href="#integration">Integrating into Your Mod</a>
3. <a href="#other-triggers">Using Other Triggers for Snowfall</a>

Mod Description<a name="description"></a>
---------------

This mod adds falling snow particles on the map, visible when zoomed in close to terrain. It's tied to the game's winter severity system, meaning the effect will appear during cold months over spots on the map where there's seasonal snow.

The effect was originally developed for **Godherja: The Dying World** - a popular fantasy total conversion - [check it out on Steam Workshop](https://steamcommunity.com/sharedfiles/filedetails/?id=2326030123).

This upload is intended for vanilla CK3 and other mods. Don't use it together with **Godherja** in the same playset.

**Mod files are up-to-date as of CK3 v1.8.1 (Robe).**

**Does not require starting a new game.** Can be safely added or removed without affecting your savegame.

**Compatible** with any mods that don't modify shaders. In practice this means most GUI, localization and gameplay mods - basically, most things aside from terrain shader mods and total conversions.

<!--If you are a modder interested in integrating this effect into your own total conversion, check out this repo on GitHub for code and integration steps.-->

Special thanks to my fellow **Godherja** team members and to the community of **CK3 Mod Co-op**.

**Happy Holidays!**

Integrating into Your Mod<a name="integration"></a>
-------------------------
This effect is very easy to integrate into a larger mod. Its implementation consists of a couple of custom shader files, several minor changes to vanilla shaders, and a single custom snowfall layer texture.

If your mod doesn't modify any shaders (doesn't have its own `gfx/FX`), you can simply copy the contents of [`mod/`](https://github.com/terrapass/ck3-mod-snowfall/tree/master/mod/) directory (except for `descriptor.mod` and the thumbnail) into your mod's file structure, and you're done.

If your mod *does* have its own shaders, follow these steps:

1. Copy [`gfx/map/environment/gh_snow_layer_1.dds`](https://github.com/terrapass/ck3-mod-snowfall/tree/master/mod/gfx/map/environment/gh_snow_layer_1.dds) into your mod's directory. Feel free to rename it as you see fit, or place it into a different folder - just don't forget to also change the path in [`gfx/FX/gh_snowfall.fxh`](https://github.com/terrapass/ck3-mod-snowfall/tree/master/mod/gfx/FX/gh_snowfall.fxh#L17) later if you do. In case you decide to edit the texture itself, keep in mind that only its alpha channel is currently used by the snowfall shader.

2. Copy every `.shader` and `.fxh` file from [`gfx/FX/`](https://github.com/terrapass/ck3-mod-snowfall/tree/master/mod/gfx/FX/), which you don't already have in your own mod, into your mod's `gfx/FX/`. This should include the two custom files provided by this mod - `gh_snowfall.fxh` and `gh_atmospheric.fxh`.

3. Manually merge in changes to shader files that already exist in your mod from this mod's versions of these files.

For convenience, all the changes in shader code, compared to vanilla, are marked with `MOD(godherja-snowfall)` comments -
you can just search for this string and copy/replace every piece of code surrounded by this comment.

In essence, the only changes this mod needs to make to vanilla shaders are:
* To replace every inclusion of `"jomini/jomini_fog_of_war.fxh"` in the game's shader files with `"gh_atmospheric.fxh"`.
* To replace every call to `ApplyFogOfWar()` and `ApplyFogOfWarMultiSampled()` in the game's shader code with a call to `GH_ApplyAtmosphericEffects()` of a similar signature.

Using Other Triggers for Snowfall<a name="other-triggers"></a>
---------------------------------
In theory it's possible for your mod to use a different condition for snowfall visibility either in addition to or completely instead of the vanilla winter system.

To do that, you can replace the call to `GetWinterSeverity()` in [`GH_ApplySnowfallEffect()`](https://github.com/terrapass/ck3-mod-snowfall/tree/master/mod/gfx/FX/gh_snowfall.fxh#L90) with any custom call returning a number between `0.0f` and `1.0f` for any given pixel.
In particular, using a literal value of `1.0f` there will make the snowfall always visible, given appropriate camera angles.

If for your mod you decide to work on a more complex condition for triggering snowfall, it might be a good idea to modify vanilla's `GetWinterSeverity()` (see `dynamic_masks.fxh` in vanilla) instead, as it'll also give you the nice terrain and object snowiness effect, in addition to triggering the snowfall.

A practical example of the snowfall (as well as vanilla winter snow) triggering based on a custom condition *might* be available in a future version of Godherja, at which point it will be briefly described in this section, so keep an eye out for that.

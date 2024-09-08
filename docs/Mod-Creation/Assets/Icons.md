# Icons

## Overview
Making icons is really cool, and one of the easier things to do for Risk of Rain 2 modding. Very little coding is required to make it work, so if you ever feel like you're struggling with making the 3d model or skills, take a break and try making some icons instead.

We'll go over stuff like
- Skin Palette Icons
- Ability Icons
- Expansion icons
- Eventually survivor lobby icons.

## Tools
### Free Software (Try these first)
- [Photopea](https://www.photopea.com/)
- [Gimp](https://www.gimp.org/downloads/)

### Paid Software
- [Aseprite](https://www.aseprite.org/), (Also available on Steam, more so for pixel art but good if you have it)
- [IcoFX](https://icofx.ro/)
- [Photoshop](https://www.adobe.com/), (Honestly, only use this if you already have it. It costs a lot)

Feel free to use any other software you find. To check if it will work, make sure you can do a couple of things
- Export to PNG (Super Important)
- Adjust canvas size (Most icons are 256 x 256, exceptions will say otherwise)
- Choose colors in a variety of ways (RGBA, Hexadecimal etc.)
> At a bare minimum color must be able to be chosen via RGBA value.

Here is an [RGBA Image Color Picker](https://colorcodefinder.com/) That has been found to be useful.

## Importing Icons to Unity
It's really simple:

- Export icon to PNG file

<img src="https://github.com/user-attachments/assets/530a8d28-b20c-42ad-88ef-5aef9406f626" width="300">

- Import into desired Unity folder (Drag and drop)

<img src="https://github.com/user-attachments/assets/62cf1a8b-563e-4290-9128-749e05d0b7c5" width="300">

<img src="https://github.com/user-attachments/assets/1ad3ab5c-6aae-4fe3-8642-c4d69e948b28" width="300">

- Change Texture Type to 2D Sprite
- Change Compression to None

<img src="https://github.com/user-attachments/assets/d0d767d5-0686-4828-9501-9f61ed499f8b" width="500">
<img src="https://github.com/user-attachments/assets/ffd74a9c-c0c9-4696-ab92-58317c38aeab" width="500">

- Apply Changes
- Double check the icon name. Typically we name them as ```texAbilityNameIcon.png```, ```texDefaultSkin.png``` or ```texMasterySkin.png```
- Double check that the icon is included in your Asset Bundle and Rebuild your bundle.

> Note: If you for some reason want to use an icon size such as 128x128. You will want to adjust Pixels Per Units to 50. If you want half the resolution, halving the pixels per unit as well is a decent rule.

## Loading Icons into a Mod

If you do not have an asset bundle I recommend look at the [Henry Tutorial's](https://github.com/ArcPh1r3/HenryTutorial) method of loading assets from an asset bundle. It is well done and makes loading assets from your asset bundle a bit easier and safer.

First you need to double check if you have rebuilt your asset bundle with your icon within it, and have moved the bundle into the right file directory. 

Create or have a Sprite field and then load from the asset bundle;

<img src="https://github.com/user-attachments/assets/447447a6-2531-4a30-a127-acae197f0eff" width="500">
<img src="https://github.com/user-attachments/assets/2dc2e21c-7500-4030-97cc-90c599844425" width="500">


This expansionDef variant shows two methods of using icons, the addressables and then importing an icon from our asset bundle

<img src="https://github.com/user-attachments/assets/bb1d394c-bb17-45d7-bbb2-fcfc264566c3" width="1000">

## Color Notes
Quick Notes about colors, they have:
- Hue - What color is it.
- Saturation - How intense is that color.
- Value - How close it that color to black or white. (0->100% is black->white)
- Alpha - How transparent is the color as a whole (For most icons this is set to the max of 255)

## Skin Palette Icons

Feel free to download any of the palettes on this page (I recommend doing so), the middle would be easiest to work with due to higher contrasting colors.

![ProvidencePalette](https://github.com/user-attachments/assets/e940e639-83c8-40fe-99de-76136413e6b0)
![PaletteTemplate](https://github.com/user-attachments/assets/dc2b074c-4120-41d0-a7a1-57ca2e912d76)
![MithrixPalette](https://github.com/user-attachments/assets/a10bc3ea-da4b-4acd-8647-b6d3ae72c9f2)

Skin icons do a lot for your character and tell a lot of information very simply. The skin icons are closer to survivor color palettes with the following colors and typical locations:
- Primary (North/Up)
- Secondary (West/Left)
- Tertiary (South/Down)
- Accents (East/Right)

Skin palletes don't always follow the same orientation but typically try keep this pattern. For instance, look at our template palette;

![PaletteTemplate](https://github.com/user-attachments/assets/dc2b074c-4120-41d0-a7a1-57ca2e912d76)

It is very clear the locations of primary, secondary colors etc. Comparatively, Providence's palette appears to be rotated 90 degrees to the left.

![ProvidencePalette](https://github.com/user-attachments/assets/e940e639-83c8-40fe-99de-76136413e6b0)

The palette still looks good and maintains the pattern while differing itself from others. This *can* be a useful trick when used sparingly. 

### Making Your Own
If you've downloaded one of the palettes from this page, then it will be as simple as filling in each section with a bucket tool. It is recommended to use RGBA values, the color picker provided can help you find colors from specific images if you love them that much. 

Remember that these are related heavily to the survivor that you make so try not to put anything that doesn't make sense for your character. The benefit of making these skin palette icons is that at any time, you can open them up and snag the colors when painting your 3d character model.

When recoloring the palettes, the inner colors are nice and bold, with higher saturation. The borders are relative to the inner colors, typically being a tiny bit less saturated and having higher value. 

On the left is an inside base color. On the right is an outside border for the same color

<img src="https://github.com/user-attachments/assets/36afd300-b790-4c31-aba3-93358a97b013" width="250">
<img src="https://github.com/user-attachments/assets/420e9230-b521-477d-a6ef-becd26e4d3cb" width="250">

## Ability Icons
Ability icons are also really important, they inform the player as to how their abilities should either work or be from a single picture. 

Let's not go too overboard and create some placeholder icons, if you're already a decent->great artist then you might be able to make the final ability icon right off the bat. However, depending on where the ability is in development, you may not want to put a bunch of effort just yet.

![texShotgun](https://github.com/user-attachments/assets/33222842-420d-43e7-8ac1-0b275154a7e2)

Notice a few things about the ability icon:
- It takes up the entire canvas
- Part of the icon is behind the border while other are over the border. This gives a slight illusion as if the ability is 3d and coming out of the image.
- The colors are related to the survivor in a hierarchy. Ability identity first, survivor identity Second. This ability tells you that you are firing something green/life related before telling you that it comes from a specific Survivor. If the ability were to have a survivor involved, for instance firing a flare into the sky, that would be one of the few times you may want the survivor to appear. 
- There are many ways of portraying an ability that do and don't involve a survivor.
- This also tells you a lot about the ability, it may not surprise you if I were to say that this icon was for a shotgun ability.

Let's do some low effort examples that break rules but allows us to get an idea of what our abilities will do. 
From Left to right: Grenade, Fire Grenade, Mortar

![texGrenade](https://github.com/user-attachments/assets/6ad8e340-6a5f-499c-86a2-8ada62f1830f)
![texSuperGrenade](https://github.com/user-attachments/assets/bb9698f9-0a34-4649-bed3-40f804591e93)
![texMortar](https://github.com/user-attachments/assets/8d6a2292-ab4b-423e-9e66-02f15b5921b7)

These aren't great but they're not meant to be great, they're meant to signal to the developer what ability the Survivor currently has.

## Expansion Icons
Okay so expansion icons are the ones that pop up in the lobby and tell you that you're using content from a specific expansion/mod. These are not necessary for many mods but for larger mods that bring in multiple survivors, locations and items, it can be nice to have something that represents all that hard work.

Expansion Icons are "weird" so to speak for a few reasons as they have.
- 512 x 512 canvas size
- A primary foreground shape
- Translucent detailed background
- Transparent background
- No border on the canvas but an outline around the central shape

> Note: Remember that when you import to Unity, you will have to increase Pixels Per Unit to 200 due to the canvas size increase

<img src="https://github.com/user-attachments/assets/59ad3b29-bfc8-4b3e-9cf6-6db493c74b51" width="250">
<img src="https://github.com/user-attachments/assets/f3ed6fa5-37ce-4028-b31d-ed0e44b2de0b" width="250">

When you make your own try keeping a consistent color throughout the entirety of the expansion icon. If it is blue, have the foreground object be blue, with translucent blue background details.

### Translucent Details
Because these icons have transparent and translucent background details, it may prove to be useful to have a dark background layer that you hide before exporting to the final product. These details are relatively simple, it can be some connected circles, 3 lines in a claw mark fashion, or in the example above an assortment of broken planets and moons.

For the translucent layer of the background an Alpha value of around 63 -> 186 can work, this depends on the color you are choosing for your expansion.
- Brighter colors such as yellows and blues you can get away with a higher Alpha closer to 186.
- Reds and Greens are a bit bolder even when translucent and should be pushed closer to an Alpha of 63.
- For reference the ```Survivors of the Void``` icon uses an Alpha of 61.

## Survivor Profile Lobby Icons
Coming soon...
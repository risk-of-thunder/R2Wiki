# Icons

## Overview
Making icons is really cool, and one of the easier things to do for Risk of Rain 2 modding. Very little coding is required to make it work, so if you ever feel like you're struggling with making the 3d model or skills, take a break and try making some icons instead.

We'll go over stuff like
- Skin Palette Icons
- Ability Icons
- Expansion Icons
- Artifact Icons
- Eventually survivor lobby icons.

## Tools
### Free Software (Try these first)
- [Photopea](https://www.photopea.com/)
- [Gimp](https://www.gimp.org/downloads/)
- [Aseprite](https://www.aseprite.org/), (Available on Steam but also has a github!)

### Paid Software
- [IcoFX](https://icofx.ro/)
- [Photoshop](https://www.adobe.com/), (Honestly, only use this if you already have it. It costs a lot)

Feel free to use any other software you find. To check if it will work, make sure you can do a couple of things
- Export to PNG (Super Important)
- Adjust canvas size (Most icons are 256 x 256, exceptions will say otherwise)
- Choose colors in a variety of ways (RGBA, Hexadecimal etc.)
> At a bare minimum color must be able to be chosen via RGBA value.

Here is an [RGBA Image Color Picker](https://colorcodefinder.com/) That has been found to be useful. (This website also has converters for RGBA to Hexadecimal etc.)

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

## Artifact Icons
These are similar to expansion icons, where they make use of both bold full-fledged colors and translucent colors but have details that can make them tricky to design. 

Breakdown:
- 64 x 64 canvas size
- These icons do not take up the entire canvas
- Enabled and Disabled versions
- Borders with Translucent outlines
> Having a dark background layer can help picture what the icon will look like in game. But it may alter the colors if you draw with the background on 

If you already know how and just need color reference here are some resources:

<img src="https://github.com/user-attachments/assets/0c51650a-5527-48e3-8ebf-8bd25e31d6fc" width="250">
<img src="https://github.com/user-attachments/assets/3e6bc83e-32b9-4f3a-99b0-2dab6210c327" width="250">

#### Disabled Icon Colors
| Color | RGBA |
|:-----:|:-----|
| Grey             | `74, 60, 66, 255` |
| Black            | `16, 18, 16, 244` |
| Translucent Grey | `2, 0, 0, 62`     |

##### Enabled Icon Colors
| Color | RGBA |
|:-----:|:-----|
| Highlight Pink            | `245, 231, 245, 255 `|
| Pink                      | `231, 199, 230, 255` |
| Dark Pink                 | `216, 168, 216, 255` |
| Translucent Dark Purple   | `66, 49, 122, 206`   |
| Translucent Purple        | `14, 4, 96, 137`     |
| Translucent Light Purple  | `14, 4, 96, 80`      |

> It is also possible to get the Translucent Dark Purple by painting a Purple of (`72, 54, 133, 190`) on top of the Translucent Grey

### Style Matching
Something nice to try to do is style matching. This means making artifact icons that feel like they already are a part of the game. 

Risk of Rain icons tend to stay very simple. Squares, circles, or adjacent shapes. There are those that get more complex (Devotion and Delusion) but simple is the goal (Evolution and Honor). If you decide/have an idea for an artifact's shape and design that does not match the style of Risk of Rain, still go ahead and make it. Having multiple versions of these icons can help you make minor or major changes in the future as you see fit.

### Disabled Icons
Try making an artifact icon by making the disabled version first. Why? Because by doing so we use fewer colors, can focus on the silhouette of the artifact more and can make changes more quickly without ripping our hair out over things we do and don't like about the design.

When doing your borders and can control how pixel perfect your border is, remember that the border is a circle rather than a square fit around your shape.

<img src="https://github.com/user-attachments/assets/72281ffb-a29f-4ce4-ba6a-32dad72bb44d" width="250">
<img src="https://github.com/user-attachments/assets/3b153d69-3ace-4d3a-bfb0-fdeea50528a9" width="250">

Example: Disabled Artifact of Glory

> Reminder: The entire canvas is not used for the artifacts; the artifact just sits in the center of our canvas as much as it can.

### Enabled Icons
If you made the disabled version of your icon first, this gets to be pretty simple. Color replacement and then a few more borders.

#### Color Evolution
- Grey -> Pink
- Black -> Dark Pink
- Translucent Grey -> Translucent Dark Purple

When handling overlapping shapes, you may have to manually add in the color transition rather than relying on an outline tool

Example: Left - Enabled Artifact of Glory, Right - Enabled Artifact of War

<img src="https://github.com/user-attachments/assets/773e506c-162c-4894-9218-10ccf4b1df32" width="250">
<img src="https://github.com/user-attachments/assets/8c162163-9785-42e0-981f-61d436274ba6" width="250">

> Note: In either of these artifacts, highlights are not involved. Your artifacts can have highlighting, but it is not a requirement.

## Survivor Profile Lobby Icons
To start, download the [Body Icon Template](https://discord.com/channels/562704639141740588/565065765452120064/1309343902142304317) from the Risk of Rain 2 Modding Discord. It's also pinned in the #character-creation channel.

This section will use GIMP, but should be possible in other software. As an example, we're going to use the blue Lemurian icon already present and add the signature blue outline other lobby icons have to the best of our ability. This process can be copied exactly for custom survivor icons, just follow the instructions in the Discord message to create your own icon to use.

Open the .psd file using GIMP.

First, you'll see the following prompt. Selecting either option is fine and is up to personal preference.

<img src="https://github.com/user-attachments/assets/5e1ef5ff-696b-4c99-bca1-a3d2c85acf4c" width="400">

Next, click here to show the Lemurian icon we'll be working with. You'll see an Eye icon appear once you do, and the image will be visible in the center.

<img src="https://github.com/user-attachments/assets/1b315e78-4d94-43f6-9081-ce54ee2734b1" width="400">

Click on Layer 10, then right click on the main image in the center and choose Layer > Layer to Image Size.

On the left side, change the Active foreground color to the following:

| Color | RGBA |
|:-----:|:-----|
| Light Sky Blue Outline | `146, 178, 236, 255 `|

Press U to enable the Fuzzy Select tool, and set Threshold on the left to 255.

<img src="https://github.com/user-attachments/assets/05b673ca-f28f-477c-baa4-f273c7cdbd82" width="200">

Click anywhere on the Lemurian to select the entire thing, and it should now have a black and white dashed outline.

Here's the process to create an outline that's the same every time.
- Right click on the image, then choose Select > Grow, and grow the selection by 9 pixels.
- Right click again, this time choosing Select > Border, setting it to the following:

<img src="https://github.com/user-attachments/assets/13a565e1-31a9-4599-9cf2-aad37819d69f" width="400">

- Select the Bucket Fill tool using Shift+B and click inside the selection. You should now have a nice outline around the lemurian!
- Press Ctrl+Shift+E to open the export menu, and change the name to texSurvivorIcon.png or something similar.
- Click Export, then Export again in the new popup, and you should now have a proper lobby icon!

<img src="https://github.com/user-attachments/assets/374195e6-3149-4d43-8dd3-a257e1721c3b" width="300">

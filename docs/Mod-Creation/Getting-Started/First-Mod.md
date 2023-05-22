# First Mod

## What you need

- Risk of Rain 2
- <a href="https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community&rel=16" target="_blank">Visual Studio Community</a>
- Make sure that when installing Visual Studio that the **.NET desktop development** workload is ticked

![netstandard2.0 visual studio installer](https://github.com/risk-of-thunder/R2Wiki/blob/master/media/netstandard2.0Framework.png?raw=true)

## Install BepInEx, HookGenPatcher and R2API

- BepInEx is a tool that allow us to load the mods into the game.

Follow the instructions [here](https://risk-of-thunder.github.io/R2Wiki/Tools/BepInEx/).

- You also need HookGenPatcher, it allow us to generate a dll assembly (Name is usually prefixed with `MMHOOK_`) for hooking the game methods.

Download and follow the instructions [here](https://thunderstore.io/package/RiskofThunder/HookGenPatcher/).

- The boilerplate mod uses some R2API modules as dependencies, it provides helper methods to quickly integrate custom additions into the game.

Download [R2API Core](https://thunderstore.io/package/RiskofThunder/R2API_Core/).

R2API Core is a dependency of R2API Items and R2API Language.

Download [R2API ContentManagement](https://thunderstore.io/package/RiskofThunder/R2API_ContentManagement/).

R2API ContentManagement is a dependency of R2API Items.

Download [R2API Items](https://thunderstore.io/package/RiskofThunder/R2API_Items/).

Download [R2API Language](https://thunderstore.io/package/RiskofThunder/R2API_Language/).

For installing them just drop the dll files into `BepInEx/plugins`

## Getting the boilerplate

Download the boilerplate [from here](https://github.com/risk-of-thunder/R2Boilerplate/archive/master.zip).

Extract it.

## Building the Boilerplate

Open the `.sln` solution file with Visual Studio. Now build the solution:

![](https://raw.githubusercontent.com/risk-of-thunder/R2Wiki/master/media/bepinex/build_solution.png)

## Put the mod in the game

Find the `ExamplePlugin\bin\Debug\netstandard2.0\ExamplePlugin.dll` on your own machine.

Copy the `ExamplePlugin.dll` to your `Risk of Rain 2/BepInEx/plugins/` folder.

## Testing the mod

If its your first time ever launching the game with the latest BepInEx Pack, you may have notice the BepInEx GUI has a console, it has multiple features that could be useful to you:

- Pausing / Resuming the game process

- Copying / extracting some log entries by selecting them (you can drag) and pressing Ctrl+C

- Filtering log entries by text or their log level

- Filtering by mod name

- Can close the game and the GUI very quickly by pressing CTRL + F5 while the GUI is focused

- [Full feature list available at](https://github.com/risk-of-thunder/BepInEx.GUI)

The console overall is very useful for debugging your code alongside the Visual Studio debugger if you follow this [tutorial](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Debugging-Your-Mods/)

When logging stuff to the console, don't forget to use the dedicated `Log` class available in the boilerplate!

Once the game launched:

- Go in singleplayer.

- Press F2. Our custom item should be spawning.


# To go further

## The sidebar is very useful for navigating the wiki (please navigate it through the [github io page](https://risk-of-thunder.github.io/R2Wiki))

## Must see pages:

- ## [Debugging your mods](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Debugging-Your-Mods/)

- ## [C# Assembly References (aka how C# .dll file dependencies work)](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Assembly-References/)

- ## [Analysing / Reading the game code](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Code-Analysis/)

- ## How to load / modify any game assets

    - ### [Explaining the concept of prefabs](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Developer-Reference/Prefabs/)

    - ### [Load game assets / prefabs](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Developer-Reference/Addressables-Assets-Keys/)

## [Hooks: For modifying the game code](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Hooking/)

## Understanding Unity Principles

- ### [MonoBehaviour](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Unity-and-MonoBehaviour/)

- ### [Prefabs](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Developer-Reference/Prefabs/)

## Github

This step isn't strictly necessary but it is good practice to use source control and the most commonly used source control with C# in Visual Studio is git.

You should be using this and synchronising your repository with github.

To do this from Visual Studio follow these simple steps in this link: https://github.com/github/VisualStudio/blob/master/docs/getting-started/authenticating-to-github.md

If you encounter any problem following this tutorial or have suggestions to make it better, feel free to edit the wiki page directly and drop a message in the [modding discord](https://discord.gg/5MbXZvd)
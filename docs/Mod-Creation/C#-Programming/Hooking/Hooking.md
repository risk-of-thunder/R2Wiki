# What is Hooking?

Hooking ([MonoMod](https://github.com/MonoMod/MonoMod)) or Patching ([Harmony](https://github.com/pardeike/Harmony)) is a way to modify the behavior of a .NET assembly. While the majority of the time in the context of Risk of Rain 2 modding this means to modify the RoR2 codebase, it can also be used to modify another mod or the C# part of Unity itself.

## MonoMod

MonoMod generates a file per target assembly, e.g., `MMHOOK_RoR2.dll`, which when referenced in a project introduces the `IL` and `On` namespaces. These allow a very quick and convenient way to reference which method one wants to modify.

The `MMHOOK_RoR2.dll` file is maintained by the community and uploaded to nuget for each game update. While updates take place within a few hours of a new game update, one can also manually generate the file with a tool provided by MonoMod, called [MonoMod.RuntimeDetour.HookGen](https://github.com/MonoMod/MonoMod/blob/master/README-RuntimeDetour.md#using-hookgen).

Alternatively, with `HookGenPatcher` installed in your modding profile, launching modded will generate this file which can then be found at a specific directory depending on the mod manager one uses:

* R2Modman: `%appdata%\r2modmanPlus-local\RiskOfRain2\profiles\<YOUR_PROFILE>\BepInEx\plugins\MMHOOK\MMHOOK_RoR2.dll`
* Gale: `%appdata%\com.kesomannen.gale\riskofrain2\profiles\<YOUR_PROFILE>\BepInEx\plugins\MMHOOK\MMHOOK_RoR2.dll`

The HookGen tool by MonoMod can also be used to generate an MMHOOK file for any other assembly, but unless one intends to heavily make use of it, it is also possible and more convenient to create custom hooks at runtime.

## Harmony

Harmony does not rely on a generated file and as such has no dependency issues. Instead it uses attributes that mark the methods containing the patching code. However, this means that unlike MonoMod, one cannot conveniently autocomplete the patching signature and has to write it manually.

# Types of hooks

There are two types of hooks.

One type intercepts the call to the target function (called `orig` in MonoMod) and allows one to write C# code before or after the method's execution, or even prevent `orig` from running at all, effectively completely overwriting what that method is supposed to do. In MonoMod this is called an `On Hook`, while Harmony has the `Prefix` and `Postfix` attributes as two distinct behaviors. While this does not modify the internals of `orig`, it is a very easy type of hook because any before/after code is written in C#.

The other hook type directly modifies the code the target method executes, but it requires an understanding of [Common Intermediate Language](https://en.m.wikipedia.org/wiki/Common_Intermediate_Language), or CIL, since it is very different from the C# code one is normally used to. In MonoMod this is known as an `IL Hook`, while Harmony uses the `ILManipulator` attribute.

Another key difference between these two types is that multiple On hooks for the same target method create a chain of hooks, all independent of each other, while multiple IL hooks modify the same code instructions in place which makes it easier to create conflicts. The bottom line is that unless one needs to use an IL hook, On hooks are generally preferred.
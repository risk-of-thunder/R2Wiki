### Requirements
* - [Visual Studio Community](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community) (recommended)*
* - dnSpy**
* - RoR2
* - BepInEx
* - [R2API](https://thunderstore.io/package/tristanmcpherson/R2API/)
* - [.NET SDK 2.0](https://dotnet.microsoft.com/download/visual-studio-sdks)
* - Basic knowledge of C#  OR  a willingness to learn

_*Any IDE that can handle C# is fine of course. But for newcomers VS community is recommended. A guide to setup VS Code (**which is not community**) can be found [here](https://github.com/risk-of-thunder/R2Wiki/wiki/Visual-Studio-Code)_

_**Alternatives to dnSpy are dotPeek, ILSpy, or any other c# decompiler_

### Tutorial

## Step 1 : Setting up your file

Using Visual Studio, create a new project.
It will be a C# Class Library (.NET Standard)

## Step 2 : Beginning Code

To begin your mod, copy and paste the below, substituting for your own names where suitable.
```c#
using BepInEx;
namespace MyUserName
{
    [BepInDependency("com.bepis.r2api")]
    //Change these
    [BepInPlugin("com.MyCompanyName.MyModName", "My Mod's Title and if we see this exact name on Thunderstore we will deprecate your mod", "1.0.0")]
    public class MyModName : BaseUnityPlugin
    {
        public void Awake()
        {
        }
    }
}
```


## Step 3 : Setting up your project

Now, you will need to add the references for your project!
Note; You must have installed BepInEx and run the game at least once.

Go to `Project` -> `Add Reference` -> select `Browse`

Navigate to the bepinex folder of your risk of rain installation. Go through the`core` and add each item.

Then go to the `plugins` folder and add all items in the `r2api` folder

Then, do the same for the main game assemblies: these are in your risk of rain folder: `Risk of Rain 2\Risk Of Rain 2_Data\Managed`
In there, select 'UnityEngine', 'UnityEngine.Networking', 'UnityEngine.CoreModule' and 'Assembly-CSharp', click "Add"

Finally, click "OK", this will apply your references (May take quite a while, be patient.)

If you're missing the `On.` and `IL.` namespaces, make sure you have https://thunderstore.io/package/RiskofThunder/HookGenPatcher/ installed and have ran the game at least once. After that, add a reference to `MMHook_Assembly-CSharp.dll` from the `BepInEx/plugins/MMHook` folder to your project.

#### Adding References

If you're new to C#, adding references to DLLs will add XML lines in the `.csproj` file for your project that look like this:
```xml
  <ItemGroup>
    <Reference Include="0Harmony">
      <HintPath>C:\Program Files (x86)\Steam\steamapps\common\Risk of Rain 2\BepInEx\core\0Harmony.dll</HintPath>
    </Reference>
    <Reference Include="Assembly-CSharp">
      <HintPath>C:\Program Files (x86)\Steam\steamapps\common\Risk of Rain 2\Risk of Rain 2_Data\Managed\Assembly-CSharp.dll</HintPath>
    </Reference>
  </ItemGroup>
```

These lines can be added manually to the `.csproj` file, and the `HintPath` directives can be relative instead of absolute (e.g. `..\libs\0Harmony.dll`), which makes collaborating on mods with others easier.

**If you cannot add a Reference due to the button not being available, please make sure you have .NET Desktop Development installed from the Visual Studio Installer under Workloads**
[Image showing the install. ](https://i.imgur.com/6rkVz7I.png)

## Step 4 : Using ...

At the very top of your file, before the namespace, add a few empty lines.

In these lines, write `using BepInEx;` and `using RoR2;`
This tells the computer what code to pull from elsewhere for us to use, in this case it's the BepInEx ModLoader and Risk of Rain 2.

## Step 5

Open Risk Of Rain 2_Data/Managed and navigate to Assembly-CSharp.dll, open this with dnSpy

Once you've opened dnSpy, there will be a bar on the left, if you click on Assembly-CSharp.dll, then you can find a list of all existing methods in the game. Alternatively you can search for something with the search icon next to "start" in the top center.

For the purposes of this mod, we will be finding `EntityStates.Huntress.ArrowRain.OnEnter()`

## Step 6 : Coding, Functions and Curly Brackets

Now, back in our Visual Studio file, we can begin modding.

`Awake()` is a function that is called when the game begins, this means that all of the code in between the following two curly brackets will run when the game starts.

Test this out:
```c#
public void Awake()
{
    Logger.LogMessage("Loaded MyModName!");
}
```

## Step 7

Press F7 to 'build' your project, this means compiling your work into a .dll file that the computer can read.
Find the folder that your visual studio project is saved to and search for a 'MyModName.dll', this can be placed in BepInEx/plugins to be run as a mod!

this .DLL is usual in the obj/debug/netstandard2.0 folder.
## Step 8 : Hooks

Alright, now you know all the basics, let's start modding.

A "Hook" is basically a piece of code that tells BepInEx where we want our mod to start executing, in this case it's the Huntress's ArrowRain Function (or whatever you found with dnSpy)

A very basic hook goes as follows:
```c#
On.[Function you want to edit] += (orig,self) =>
{
    // [The code you want to run]
    orig(self);
};
```
'orig' is the original function in the game, if you don't want to outright replace the function, you need to call orig(self) at the end of your function, or at the start if you want to run after it!

'self' allows you to access member variables, with dnSpy, these are the variables you see at the very bottom of the file, that aren't in any particular function.

So, Our code will be 
```c#
public void Awake()
{
    Logger.LogMessage("Loaded MyModName!");
    On.EntityStates.Huntress.ArrowRain.OnEnter += (orig, self) =>
    {
        // [The code we want to run]
        Chat.AddMessage("You used Huntress's Arrow Rain!");
        // Call the original function (orig)
        // on the object it's normally called on (self)
        orig(self);
    };
}
```
## Step 9 : Editing Values

Alright, now we know where to put code that we want to run when a huntress uses Arrow Rain!

We also added a logging message, `Chat.AddMessage("You used Huntress's Arrow Rain!")`, as a basic test


Then for anything else you want to do,
Simply replace the above [Code We Want To Run] with whatever it is you want to run.
Replace the method we hook into

**ALWAYS call `orig(self)`** (Unless you really mean for the original method to not be called)

## Step 10 : Github

This step isn't strictly necessary but it is good practice to use source control and the most commonly used source control with C# in Visual Studio is git. You should be using this and synchronising your repository with github. To do this from Visual Studio follow these simple steps in this link: https://github.com/github/VisualStudio/blob/master/docs/getting-started/authenticating-to-github.md
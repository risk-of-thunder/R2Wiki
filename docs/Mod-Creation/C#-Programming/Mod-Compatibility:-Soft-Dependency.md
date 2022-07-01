# Mod Compatibility: Soft Dependency

# Implementation
Follow this guide step-by-step.

## Step 1: You need BepInEx
Your mod needs to be BepInEx Plugin to depend on other BepInEx plugins.

## Step 2: Add the `BepInDependency` attribute
Add the the attribute on top of your BaseUnityPlugin class. Use `BepInDependency.DependencyFlags.SoftDependency` for the 2nd argument. For example, we will try to access the API of `OtherMod`. 
```csharp
[BepInDependency("com.chen.othermod", BepInDependency.DependencyFlags.SoftDependency)]
public class HelperPlugin : BaseUnityPlugin
{
    // code
}
```

## Step 3: Make a wrapper class
Adding a soft dependency from a mod does not end there. You need to make a wrapper class that will house all the API from a mod that you are soft-dependent on. Let's make a basic wrapper with all the necessary code. First, create a new file as **this needs to be in a separate `.cs` file**.
```csharp
// OtherModCompatibility.cs
public static class OtherModCompatibility
{

}
```

## Step 4: Add the `enabled` property
This property is essential when invoking API methods. This will help your mod prevent itself into accessing the assembly of the soft dependency mod directly. **Accessing a non-existent assembly brings a bad error!**
```csharp
public static class OtherModCompatibility
{
    private static bool? _enabled;

    public static bool enabled {
        get {
            if (_enabled == null) {
                _enabled = BepInEx.Bootstrap.Chainloader.PluginInfos.ContainsKey("com.chen.othermod");
            }
            return (bool)_enabled;
        }
    }
}
```

## Step 5: Implement the API as methods
You can now implement the API in the wrapper class. Let's imagine `OtherMod` has a public, static method called `OtherMethod` that accepts a `string` argument.
```csharp
public static class OtherModCompatibility
{
    // other properties

    public static InvokeOtherMethod(string message)
    {
        OtherMod.OtherMethod(message);
    }
}
```

## Step 6: Safely invoke the API through the wrapper
Invoke the method from the wrapper while also using the `enabled` property check. **This is important as it is safe from getting errors.**
```csharp
// attributes
public class HelperPlugin : BaseUnityPlugin
{
    // code

    if (OtherModCompatibility.enabled)
    {
        OtherModCompatibility.InvokeOtherMethod("Hello World!");
    }
}
```

## Step 7: Success
That is how one implements a soft dependency with another mod.

# Understanding the "DO NOT"
There are things that one must **not** do when trying to code with soft dependencies.

## Having `using` statements that comes from a soft-dependent mod
Do not do this or a bad error will pop from the console about missing assemblies when the soft-dependent mod is disabled. Contain the `using` statements inside the wrapper class.
```csharp
// MyHelperClassInMyMod.cs
using OtherMod.Namespace; // DO NOT DO THIS! Contain all code related to OtherMod in the wrapper!
namespace Chen.Helpers
{
    public class MyHelperClassInMyMod
    {
        // code
    }
}
```

## Moving the `enabled` check inside the wrapper class for cleaner code
The whole point of the wrapper class is to prevent your code from accessing non-existent assemblies. Moving the `enabled` check inside would defeat this purpose, and thus an error will occur when the soft-dependent mod is disabled.
```csharp
// OtherModCompatibility.cs
public static class OtherModCompatibility
{
    private static bool? _enabled;

    public static bool enabled {
        get {
            if (_enabled == null) {
                _enabled = BepInEx.Bootstrap.Chainloader.PluginInfos.ContainsKey("com.chen.othermod");
            }
            return (bool)_enabled;
        }
    }

    public static InvokeOtherMethod(string message)
    {
        // DO NOT DO THIS! Put the enabled check alongside the invocation!
        if (enabled) {
            OtherMod.OtherMethod(message);
        }
    }
}
```

## Invoking the API directly
Even with the `enabled` check, invoking the method when the soft-dependent mod is disabled will still result into an error. **Always implement the API inside the wrapper class.**
```csharp
// MyHelperClassInMyMod.cs
namespace Chen.Helpers
{
    public class MyHelperClassInMyMod
    {
        // code

        if (OtherModCompatibility.enabled)
        {
            // DO NOT DO THIS! This will still result into an error!
            OtherMod.OtherMethod("Hello World!");
        }
    }
}
```
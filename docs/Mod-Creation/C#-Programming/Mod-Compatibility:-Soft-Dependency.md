# Mod Compatibility: Soft Dependency

## `BepInDependency` Attribute
Add the attribute on top of your BaseUnityPlugin class.

Use `BepInDependency.DependencyFlags.SoftDependency` for the 2nd argument.

For example, we will try to access the API of `OtherMod`.

```csharp
[BepInDependency("com.chen.othermod", BepInDependency.DependencyFlags.SoftDependency)]
public class MyPlugin : BaseUnityPlugin
{

}
```

## Make a non nested wrapper class

This is optional but much cleaner just for the sake of project organization.

The wrapper class will house all the APIs from the mod that we are soft-dependending on.

```csharp
public static class OtherModCompatibility
{

}
```

## MethodImpl

The important thing is that anything that require elements from the soft dependencies are only used behind methods that have these two attributes.

```csharp
[MethodImpl(MethodImplOptions.NoInlining | MethodImplOptions.NoOptimization)]
```

This is due to internal details on how the c# runtime works.

If these are not here you'll most likely get exceptions thrown at you telling you the soft dependency is not loaded, even though we properly check with the `PluginInfos.ContainsKey`!

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

    [MethodImpl(MethodImplOptions.NoInlining | MethodImplOptions.NoOptimization)]
    public static void SomeMethodThatRequireTheDependencyToBeHere()
    {
      // stuff that require the dependency to be loaded
    }
}
```

Finally, some example usage.

```csharp
[BepInDependency("com.chen.othermod", BepInDependency.DependencyFlags.SoftDependency)]
public class MyPlugin : BaseUnityPlugin
{
    private void Awake()
    {
        if (OtherModCompatibility.enabled)
        {
            OtherModCompatibility.SomeMethodThatRequireTheDependencyToBeHere();
        }
    }
}
```
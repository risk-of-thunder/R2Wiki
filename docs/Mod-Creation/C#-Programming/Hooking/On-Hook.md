# Introduction

An On Hook is a type of hook that allows to run code before and/or after a target method without modifying the target method itself. For modifying the method see [IL Hook](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Hooking/IL-Hook) instead.

The hook is defined once, generally in the `Awake` of your `BaseUnityPlugin`, and it runs every time the original method is executed.

## How to use

Applying and undoing a hook looks very similar to how one subscribes and unsubscribes from events in C#, even though hooks are very different from events.

Using the `On` namespace fully qualify the method you want to hook, then type `+=` and then press `Tab` to autocomplete the hook delegate with the correct signature. For example,

```cs
On.RoR2.RoR2Application.Awake += //press Tab now
```

To undo this hook, use `-=` instead of `+=`.

## Hook method arguments

Let's hook `HealthComponent.TakeDamage(DamageInfo)` and see what the hook signature looks like.

```cs
private void Awake()
{
    On.RoR2.HealthComponent.TakeDamage += OnTakeDamage;
}

private void OnTakeDamage(On.RoR2.HealthComponent.orig_TakeDamage orig, HealthComponent self, DamageInfo damageInfo)
{
    // code that will run before the original method
    orig(self, damageInfo);
    // code that will run after the original method
}
```
`orig` is the original method in the game. If you don't want to outright replace it, you need to call it at some point in your hook. There is rarely a reason to omit calling it and doing so in this case would break the game as nothing would take damage. Omitting `orig()` just so it can manually be rewritten can also lead to [interoperability issues](#hook-chain).

`self` is the instance type calling the original method. You can use this to access member variables, e.g., in this case to check which `self.body` is taking damage. If the original method is static, there is no `self` argument.

Any further arguments in the hook are written in the same order the original method declares them, if it has any. In this case we only have the `DamageInfo`.

If we run our own code before the original, we can perform logic prior to calling the original method, and even modify the parameters of the method before it is executed. Similarly, if we run our code after the original, the original will still perform its intended task, but will also execute our code right after.

Since the original method in our example runs only on the server, it is also a good idea to surround any additional code we run in a `NetworkServer.active` check because we can't guarantee a client doesn't try to call it.

## Hook chain

Assuming that multiple mods hook a method, the load order follows LIFO (Last In First Out) priority.

`Vanilla -> Mod A -> Mod B -> Mod C`

Becomes

`C() -> B() -> A() -> Vanilla`

If B does not call orig, regardless of whether A does, neither A nor vanilla will execute.

# Examples

## Modify an input parameter

Parameters can be intercepted prior to being passed to the original method. For example, we can intercept the arguments passed to the PickupController, and instead of dropping lunar coins, we can now drop goat hoofs.

```cs
private static void PickupDropletController_CreatePickupDroplet(On.RoR2.PickupDropletController.orig_CreatePickupDroplet orig, PickupIndex pickupIndex, UnityEngine.Vector3 position, UnityEngine.Vector3 velocity)
{
    if (pickupIndex == PickupCatalog.FindPickupIndex("LunarCoin.Coin0"))
    {
        pickupIndex = PickupCatalog.FindPickupIndex(ItemIndex.Hoof);
    }
    orig(pickupIndex, position, velocity);
}
```

## Modify the result

For methods that return a type, we can run our code after orig and then change the result. For example, if we have a reviving item, we want to modify the method that checks if we're out of extra lives to also take into account ours.

```cs
private static void CharacterMaster_IsDeadAndOutOfLivesServer(On.RoR2.CharacterMaster.orig_IsDeadAndOutOfLivesServer orig, CharacterMaster self)
{
    bool result = orig(self);
    if (NetworkServer.active)
    {
        CharacterBody body = self.GetBody();
        if ((!body || !body.healthComponent.alive) || self.lostBodyToDeath)
        {
            result |= self.inventory.GetItemCount(MyRevivingItem) > 0;
        }
    }
    return result;
}
```

## Hooking a coroutine (IEnumerator)

Hooking a method that returns an enumerator is similar to a method that returns a value, except that instead of doing `return value` we do `yield return value`.

```cs
private IEnumerator RoR2Application_InitializeGameRoutine(On.RoR2.RoR2Application.orig_InitializeGameRoutine orig, RoR2Application self)
{
    // code that will run before the original method
    yield return orig(self);
    // code that will run after the original method
}
```

However, this means that our code after will be executed only after the coroutine has completed. If you want to execute code every time the coroutine resumes, you can do this instead

```cs
private IEnumerator RoR2Application_InitializeGameRoutine(On.RoR2.RoR2Application.orig_InitializeGameRoutine orig, RoR2Application self)
{
    // code that will run before the original method
    IEnumerator enumerator = orig(self);
    while (enumerator.MoveNext())
    {
        yield return enumerator.Current;
        // code that runs every time we resume
    }
    // code that will run after the original method
}
```

# Manual On Hooks

Sometimes you may need to create a custom hook, either because you're targeting a method in an assembly for which you don't have an `MMHOOK_X.dll` file, or because you want to target a property since MMHOOK does not provide hook shortcuts for those by default.

The basic signature for defining a hook is

```cs
new Hook(origMethodInfo, hookDelegate);
```

`origMethodInfo` is obtained using [Reflection](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Reflection-Crash-Course/), generally with `typeof(Class).GetMethod(nameof(Class.Method))`, but [`AccessTools`](https://harmony.pardeike.net/api/HarmonyLib.AccessTools.html) has a lot of convenience methods and already comes bundled with BepInEx, while [`R2API`](https://github.com/risk-of-thunder/R2API/blob/master/R2API.Core/Utils/Reflection.cs) also introduces its own reflection extensions.

In order to construct the correct signature for the hook delegate method, similar to the [Hook method arguments](#hook-method-arguments) section, the second parameter onward of our delegate are the type of instance method (if it isn't static) and any other arguments the original method has.

For constructing `orig` we use a delegate of either `Action<TSelf, TArg1, TArg2, ...>` if the original method has no return type, or `Func<TSelf, TArg1, TArg2, ..., TResult>` if it does.

Some examples for the orig pattern are:
* `static void Original()` -> `Action`
* `void Original()` -> `Action<TSelf>`
* `static void Original(bool[], int)` -> `Action<bool[], int>`
* `void Original(bool[], int)` -> `Action<TSelf, bool[], int>`
* `static float Original()` -> `Func<float>`
* `float Original()` -> `Func<TSelf, float>`
* etc

If the target method has a ref parameter, Action/Func cannot be used to construct orig. Instead, we need to use a delegate like so

```cs
private delegate int origDelegate(GenericSkill self, ref GenericSkill.SkillOverride skillOverride);

private void Awake()
{
    new Hook(AccessTools.Method(typeof(GenericSkill), nameof(GenericSkill.FindSkillOverrideIndex)), MyHook);
}

private int MyHook(origDelegate orig, GenericSkill self, ref GenericSkill.SkillOverride skillOverride)
{
    return orig(self, ref skillOverride);
}
```

## Example 1: Hooking a property getter

___Note___: Property getters and setters are methods under the hood.

In the following example we hook the gold reward for a character's death and increase it if the dead body was inflicted with a debuff.

```cs
private void Awake()
{
    MethodInfo getterMethod = AccessTools.PropertyGetter(typeof(DeathRewards), nameof(DeathRewards.goldReward));
    new Hook(getterMethod, OnGetGoldReward);
}

private uint OnGetGoldReward(Func<DeathRewards, uint> orig, DeathRewards self)
{
    uint result = orig(self);
    if (self.characterBody.GetBuffCount(IncreasedGoldRewardBuff) > 0)
    {
        result *= 2u;
    }
    return result;
}
```

## Example 2: Hooking a method from another mod

Hooking a method from another assembly requires adding it as a package reference in your project.

```
<ItemGroup>
    <Reference Include="ModName">
        <HintPath>path\to\file\ModName.dll</HintPath>
    </Reference>
</ItemGroup>
```

A [publicized](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Assembly-References/#publicizing-stripping-assemblies-we-dont-already-cover) version may even be more appropriate if one needs to access private types and members.

Then getting the method info can be achieved with `AccessTools.Method(typeof(ModName.ClassName), nameof(ModName.ClassName.MethodName))`.

## HookConfig

The `HookConfig` is an optional 3rd parameter when defining a manual hook that provides some further customization.

### Manually apply and undo a hook

When calling `new Hook()` the hook is enabled automatically. If you wish to control when a hook is applied, it can be done like so:

```cs
// We need to store a reference to it
private Hook myHook;

private void Awake()
{
    var hookConfig() { ManualApply = true };
    // Define the hook but leave it inactive
    myHook = new Hook(origMethodInfo, hookDelegate, ref hookConfig);

    // `myHook.IsApplied` reports current state
}

private void OnEnable()
{
    // The hook will now run whenever the method is called
    myHook.Apply();
}

private void OnDisable()
{
    // Disable the hook once again
    myHook.Undo();

    // We still retain a reference to `myHook` and we
    // can enable it again at some point in the future
}
```

### Set hook priority

The default hook order is [LIFO](#hook-chain), but it is also possible to override this with an explicit hook priority.

```cs
private void Awake()
{
    var hookConfig = new HookConfig() { Priority = 1 };
    new Hook(origMethodInfo, hookDelegate, ref hookConfig);
}
```

Hooks with positive priority are executed first, while hooks with negative priority last. Hooks that don't explicitly define a priority, including those done via the `On` namespace, default to 0. If there are multiple hooks with the same priority, their relative order is LIFO. For example, assume the following mods hook the same method in this order, each with their own priority

|A|B|C|D|E|F|
|:---:|:---:|:---:|:---:|:---:|:---:|
|0|1|1|-2|3|0|

The order in which they will be executed is

|E|C|B|F|A|D|
|:---:|:---:|:---:|:---:|:---:|:---:|
|3|1|1|0|0|-2|

# General advice

* Make sure to null check everything. If your hook crashes, it can bring the whole chain down, including orig, which affects vanilla. `master.body`, `body.master `, `body.inventory `, and `damageInfo.attacker` are some of the most frequent sources of null errors in hooks.
* There is generally no reason to undo a hook. They are expensive to toggle at runtime, while fully disabling your mod may be an exercise in futility. Especially when you have also modified any prefabs or catalogs. In order to temporarily disable a hook, use a boolean instead to determine whether to execute the hook's logic.

  ```cs
  bool runHook;

  private void MyHook(...)
  {
      if (!runHook)
      {
          orig();
          return;
      }

      // Any hook logic goes here, including
      // calling orig at the appropriate time.
  }
  ```

* If vanilla provides an event that runs in the method you want to hook, it is more preferable using that instead of making a hook, e.g. subscribing to `GlobalEventManager.onCharacterDeath` instead of hooking `On.RoR2.GlobalEventManager.OnCharacterDeath`.
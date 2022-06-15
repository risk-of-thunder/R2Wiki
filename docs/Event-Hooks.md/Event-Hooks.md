Event Hooks are implemented by the use of the On. class, which allows the user to specify the namespace of a specific method which can be used as an entry point into an object. A hook like this only needs to be defined once, usually inside Awake(). The code within the lamda expression will be executed every time the original method is called by the game. This means that this hook is not bound to a specific instance of an object, but rather to all objects of that type.

A very basic hook goes as follows:

```csharp
On.[Function you want to edit] += (orig,self) =>
{
    // [The code you want to run]
    orig(self);
};
```

'orig' is the original function in the game, if you don't want to outright replace the function, you need to call orig(self) at the end of your function, or at the start if you want to run after it!

'self' allows you to access member variables, with dnSpy, these are the variables you see at the very bottom of the file, that aren't in any particular function.

So, Our code will be 

```csharp
public void Awake()
{
    Chat.AddMessage("Loaded MyModName!");
    On.EntityStates.Huntress.ArrowRain.OnEnter += (orig, self) =>
    {
        // [The code we want to run]
        orig(self);
    };
}
```

Event hooks are incredibly useful in order to modify the behaviour of methods. If we run our own code before the original, we can perform logic prior to calling the original method, and even modify the parameters of the method before it is executed. Similarly, if we run our code after the original, the original will still perform its intended task, but will also execute our code on top of it. One problem that many people will find with this method, is that we are unable to modify the logic that exists within the method, without replacing it entirely. Failing to call `orig()` will result in the original/vanilla method never being executed.

## A little more advanced

Parameters can be intercepted prior to being passed to the games vanilla methods, for example, we could intercept the arguments passed to the PickupController, and instead of dropping lunar coins, we can now drop goat hoofs.

```csharp
private static void PickupDropletController_CreatePickupDroplet(On.RoR2.PickupDropletController.orig_CreatePickupDroplet orig, PickupIndex pickupIndex, UnityEngine.Vector3 position, UnityEngine.Vector3 velocity)
{
    if(pickupIndex == PickupCatalog.FindPickupIndex("LunarCoin.Coin0"))
    {
        pickupIndex = PickupCatalog.FindPickupIndex(ItemIndex.Hoof);
    }
    orig(pickupIndex, position, velocity);
}
```

Replacing a large method in its entirety in order to modify a single line of logic would not be advised as it will create interoperability issues with other mods; for such scenario's something like an [IL Hook](https://github.com/risk-of-thunder/R2Wiki/wiki/Working-with-IL) would be advised.

## **Delegate Chaining**

Assuming that multiple mods hook a method, load order follows LIFO(Last In First Out) priorities.

`Vanilla -> Mod A -> Mod B -> Mod C`

**Becomes**

`C() -> B() -> A() -> Vanilla`

If B does not orig(self), but A does, A will never execute, and neither will vanilla.
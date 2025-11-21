# IL Hook

IL Hooks can modify the set of instructions a method consists of in this form, thus allowing one to manipulate the method logic in ways that are not possible with [On Hooks](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Hooking/On-Hook).

## What is CIL?

[CIL](https://en.m.wikipedia.org/wiki/Common_Intermediate_Language) is the result of compiling readable C# code into another form, which can be universally processed by multiple architectures and converted into machine code targeting the current architecture at runtime, hence "intermediate language".

### The stack

A Stack is used at a low/intermediate level to process data. As explained by Wikipedia, only the value at the very top of the stack can be popped from the stack. Given a list of IL instructions, the processor will iterate its way through the operations, emitting values to the stack, and executing operations upon the stack as and when the instruction is executed. At the point of termination (return), the stack must be empty, with no more instructions or values remaining. If this is not the case, the stack is said to be unbalanced and the process will terminate with the error Invalid ILCode.

To see how the transformation from C# code to CIL affects the order of instructions due to how the stack operates, we can use a simple example.

```cs
public static float ComputeFormula(int x)
{
    return 2 * (1 + x);
}
```

The equivalent CIL would be

```
ldc.i4.2 // pushes the int32(2) onto the stack
ldc.i4.1 // pushes the int32(1) onto the stack
ldarg.0  // pushes the value of the first argument onto the stack
add
mul
ret      // method returns
```

For the sake of argument let's also assume that `x = 3`. After the first 3 instructions from bottom to top the stack contains [2,1,3]. When the `add` operation is executed, the top two values are popped from the stack, added together to produce [4], which is then pushed onto the stack resulting in [2,4]. Next, the `mul` operation pops both of these values, multiplies them together and the resulting stack contains only [8]. The method then returns, passing the value 8 to the calling method. The method has finished executing, there are no more operations and the stack is now empty, thus the stack is balanced.

[ECMA-335, Partition III (Section 3)](https://download.microsoft.com/download/7/3/3/733ad403-90b2-4064-a81e-01035a7fe13c/ms%20partition%20iii.pdf) contains information about each OpCode, e.g., how many parameters each one pops from the stack, the ordering of the values, and the return type of the operation.

## ILCursor

The `ILCursor` is an implementation by MonoMod to facilitate traversal of the stack in a user friendly manner. Its documentation can be found [here](https://monomod.dev/api/MonoMod.Cil.ILCursor.html).

The following references are widely used when working with it:

```cs
using Mono.Cecil.Cil;
using MonoMod.Cil;
```

To apply an IL Hook we use similar syntax to how one subscribes and unsubscribes from events in C#, even though hooks are very different from events.

Using the `IL` namespace fully qualify the method you want to hook, then type `+=` and then press `Tab` to autocomplete the hook delegate with the correct signature. For example,

```cs
IL.RoR2.HealthComponent.TakeDamageProcess += //press Tab now
```

To undo this hook, use `-=` instead of `+=`.

The cursor is created from the argument provided by the hook delegate.

```cs
private void SomeILHook(ILContext il)
{
    ILCursor c = new ILCursor(il);
}
```

From it one can access the list of all IL instructions with `c.Instrs`, while `c.Index` gives the current position of the cursor. Similar to a text cursor the position of ILCursor is between instructions and `c.Prev` / `c.Next` access the respective one on either side.

`Debug.Log(c)` prints the previous and next instructions while `Debug.Log(il)` prints all the instructions of the hooked method. The latter can be useful at runtime to inspect whether any instructions have already been modified from an earlier hook compared to what one sees from the decompiled assembly. If you want to see the result of your emitted instructions, the following may be more desirable

```cs
// The offsets of your emitted instructions are normally recalculated after the hook
// has been applied and until then are displayed as IL_0000. While this can make it
// easier to spot if your instructions have been emitted in the right place, it makes
// it harder to verify when branches are involved. Force this recalculation now.
il.Method.RecalculateILOffsets();
Debug.Log(il);
```

The main functionality of the cursor revolves around moving it to the right location (pattern matching) and modifying or emitting any new instructions. The following sections deal with any subtleties in both of these aspects.

It is important to note that while `c.Remove()` and `c.RemoveRange()` are available resources to remove instructions, they should be avoided as much as possible because another instruction could be referencing a removed one, leading to issues. If you want to replace an instruction, do so from `c.Prev` / `c.Next` directly, while for skipping a range of instructions use [`Br`](#conditionals-and-branches).

### Pattern matching

Using `c.Index` to directly go to the desired location in a set of IL instructions is a bad idea, since if another mod or another game update modifies any instruction prior to this, we will end up at the wrong place and any instruction manipulation will break the game. Instead, we match a certain set of instructions that mark out desired location and we let the cursor dynamically decide where that is, if it exists.

To that end we use `c.TryGotoNext()` and `c.TryGotoPrev()`, which return a bool if a match is successful while also updating the `Index` accordingly. The `GotoNext()` and `GotoPrev()` versions should be avoided since they raise a `KeyNotFoundException` error if a match has failed, which can abruptly interrupt your mod's initialization process.

When we match a block of instructions, the cursor is placed before the first matched instruction by default. If you want the cursor to be after the last matched instruction instead, you can pass `MoveType.After` as the first argument to the TryGoto search.

When matching instructions the pattern is a method call with the `Match` prefix and the name of the OpCode, e.g., `x.MatchLdarg()`, `x.MatchLdstr()`, etc. The majority of these take an argument to indicate the value related to this instruction. For example, `x.MatchLdstr("Hello")` matches loading the string "Hello" on the stack. On the other hand, `x.MatchLdstr(out string s)` matches a `ldstr` instruction regardless of its value which is also returned to the user so it can be referenced again later. This can be useful for caching a certain local variable or conditional jump target.

For invoking methods and accessing fields the argument/out value is a MethodInfo/FieldInfo, which can be obtained with reflection. However, there is another approach which can be less verbose:

```cs
x.MatchLdfld(typeof(CharacterBody).GetField(nameof(CharacterBody.bodyIndex))); // reflection
x.MatchLdfld<CharacterBody>(nameof(CharacterBody.bodyIndex));                  // generic
```

Furthermore, for methods there is both `x.MatchCall` and `x.MatchCallvirt` which are used in different scenarios, but it is recommended to use `x.MatchCallOrCallvirt` instead so you don't have to worry about using the correct one.

#### Example 1: Simple matching and modifying a value

Let's say we want to modify the Crowbar health threshold from 90% to 80%. This functionality is found in `HealthComponent.TakeDamageProcess`:

```cs
if (num >= fullCombinedHealth * 0.9f)
{
    int itemCount2 = characterMaster.inventory.GetItemCount(RoR2Content.Items.Crowbar);
    if (itemCount2 > 0)
    {
        num4 *= 1f + 0.75f * (float)itemCount2;
        EffectManager.SimpleImpactEffect(AssetReferences.crowbarImpactEffectPrefab, damageInfo.position, -damageInfo.force, transmit: true);
    }
}
```

With ILSpy from the top bar we can select to view "IL with C#", while with dnSpy we can right click on `num >= fullCombinedHealth * 0.9f` and select "Edit IL instructions..." so we can see the instructions this corresponds to.

```
IL_085a: ldloc.s 4
IL_085c: ldarg.0
IL_085d: call instance float32 RoR2.HealthComponent::get_fullCombinedHealth()
IL_0862: ldc.r4 0.9
IL_0867: mul
IL_0868: blt.un.s IL_08bb
```

With this information our hook can then be

```cs
private void Awake()
{
    IL.RoR2.HealthComponent.TakeDamageProcess += ModifyCrowbarThreshold;
}

private void ModifyCrowbarThreshold(ILContext il)
{
    ILCursor c = new ILCursor(il);
    if (c.TryGotoNext(
        MoveType.After,
        x => x.MatchLdloc(4),
        x => x.MatchLdarg(0),
        x => x.MatchCallOrCallvirt(AccessTools.PropertyGetter(typeof(HealthComponent), nameof(HealthComponent.fullCombinedHealth))),
        x => x.MatchLdcR4(0.9f)))
    {
        // Successful match and the cursor is now between "ldc.r4 0.9" and "mul"
        // `c.Prev.OpCode` returns "ldc.r4" - you rarely want to modify this
        // `c.Prev.Operand` returns the value of the opcode, if any
        c.Prev.Operand = 0.8f;
    }
    else
    {
        Logger.LogError(il.Method.Name + " IL Hook failed!");
    }
}
```

#### Example 2: Modifying a value with EmitDelegate

Sometimes the value we want to replace in an instruction can vary during runtime or the logic we need requires emiting very complex instructions, such as branching and multiple operations. In such a case, we place our cursor after the value we want to replace and emit a delegate which consumes this value and returns a similar one of the same type. The best part is that the code in the delegate is written in plain C#.

Using the same example as before, our hook with the EmitDelegate approach becomes

```cs
private void ModifyCrowbarThreshold(ILContext il)
{
    // The same pattern matching as before that puts the cursor between "ldc.r4 0.9" and "mul"

    // - The float value 0.9 is at the top of the stack, which we will consume
    // - For our logic we also need the attacker which can be obtained from the DamageInfo argument
    // - The value we return from our delegate must also be a float so we don't disrupt stack

    c.Emit(OpCodes.Ldarg_1); // Load the DamageInfo argument (Ldarg_0 is `this` since this is an instance method)
    c.EmitDelegate<Func<float, DamageInfo, float>>((threshold, damageInfo) =>
    {
        // We don't need to null check this because it's guaranteed to have a valid value by the time the relevant code has been reached
        int bars = damageInfo.attacker.GetComponent<CharacterBody>().inventory.GetItemCount(RoR2Content.Items.Crowbar);
        if (bars > 0)
        {
            return 1f - ((1f - 1f / (_crowbarScalar * (float)bars + 1f)) * _crowbarCap);
        }
        return threshold;
    });
}
```

Delegates can either return a value, as defined by Func<T>, or they can return void, defined by Action<T>. In the case of Func<T> the last type defined is the return type.

According to [jade](https://discordapp.com/channels/562704639141740588/562704639569428506/604753431894294528):

> EmitDelegate was designed to behave the same as `call` / `callvirt`, thus passing all args from stack onto the delegate just like if it was a normal method call
> thus, if you push A, B, C, your delegate should accept A, B, C in that order. It doesn't pop one by one, it "pops" the last N elements from the stack at once.

Action/Func don't support ref parameters so if the method you want to invoke includes one, define an actual method and then use it directly with `c.EmitDelegate(MyMethod)`.

#### Robust pattern matching

You should not rely on the instructions you see when you decompile the game to be the same as those when modifying a method at runtime. This can be particularly true for frequently hooked methods, such as `CharacterBody.RecalculateStats`, `HealthComponent.TakeDamageProcess`, and `GlobalEventManager.ProcessHitEnemy`. But also hardcoding certain values can break a hook when the game updates.

The following tips establish good practices to ensure the pattern matching is as dynamic as possible while failure due to mod conflicts are minimized.

##### 1. Match as little as possible to uniquely identify the correct cursor index

The more instructions that are included in your match, the more likely it is to hit a point where another mod has inserted their own instructions. For example, if you want to replace the value of a method call, just matching the call instruction should suffice. Assuming of course it does not occur multiple times in the hooked method.

```cs
if (c.TryGotoNext(
    MoveType.After,
    x => x.MatchCallOrCallvirt<Type>(nameof(Type.MethodName)))
{
    // Match found, EmitDelegate can be used now to modify its return value.
}
```

For long methods that may repeat the same pattern multiple times, you can use a "landmark" to first get the cursor index near your target pattern and then search for your pattern directly. For example, let's say we want to modify the success roll for Sticky Bomb in `ProcessHitEnemy`. `Util.CheckRoll` is used multiple times in that method, but we can narrow it down like this:

```cs
// The code where Sticky Bomb rolls for proc:
// int itemCount10 = inventory.GetItemCount(RoR2Content.Items.StickyBomb);
// if (itemCount10 > 0 && Util.CheckRoll(5f * (float)itemCount10 * damageInfo.procCoefficient, master) && (bool)characterBody2)

if (c.TryGotoNext(
        x => x.MatchLdsfld(typeof(RoR2Content.Items), nameof(RoR2Content.Items.StickyBomb)))
    && c.TryGotoNext(
        MoveType.After,
        x => x.MatchCallOrCallvirt(typeof(Util), nameof(Util.CheckRoll))))
{
    // success
}
```

##### 2. Dynamically match the index of a local variable

In a [previous example](#example-1-simple-matching-and-modifying-a-value) where we matched the formula for the Crowbar threshold, we hardcoded which local variable to load. This variable is assigned earlier in the method

```
// float num = combinedHealth;
IL_0035: ldarg.0
IL_0036: call instance float32 RoR2.HealthComponent::get_combinedHealth()
IL_003b: stloc.s 4
```

It is possible one day for new code to be added before that part, effectively shifting this variable index to 5 or greater. However, we can ensure we always have the right index by caching where that value is stored during the related `stloc` instruction.

```cs
int combinedHealthVarIndex = -1;
if (c.TryGotoNext(
    x => x.MatchLdarg(0),
    x => x.MatchCallOrCallvirt(AccessTools.PropertyGetter(typeof(HealthComponent), nameof(HealthComponent.combinedHealth))),
    x => x.MatchStloc(out combinedHealthVarIndex)))
{
    // Now when we match the Crowbar formula, we can do
    // x => x.MatchLdloc(combinedHealthVarIndex)
}
```

##### 3. Matching snippets of a pattern with gaps

Let's say that in `EntityStates.GenericCharacterMain.ProcessJump(bool)` we want to cache the local variable index for the number of Wax Quail stacks.

```
// int itemCount = base.characterBody.inventory.GetItemCount(RoR2Content.Items.JumpBoost);
IL_004b: ldarg.0
IL_004c: call instance class RoR2.CharacterBody EntityStates.EntityState::get_characterBody()
IL_0051: callvirt instance class RoR2.Inventory RoR2.CharacterBody::get_inventory()
IL_0056: ldsfld class RoR2.ItemDef RoR2.RoR2Content/Items::JumpBoost
IL_005b: callvirt instance int32 RoR2.Inventory::GetItemCount(class RoR2.ItemDef)
IL_0060: stloc.3
```

It would make sense to match the last 3 instructions, but it is possible for another mod to have already injected their own code after the GetItemCount call to also add the number of stacks of their modded item. While it may feel pedantic, it would be safer to just match the `ldsfld` and `stloc` instructions in cascading checks which does not care what else happens in the between.

```cs
int waxQuailVarIndex = -1;
if (c.TryGotoNext(x => x.MatchLdsfld(typeof(RoR2Content.Items), nameof(RoR2Content.Items.JumpBoost)))
    && c.TryGotoNext(x => x.MatchStloc(out waxQuailVarIndex)))
{
    // success
}
```

##### 4. Use c.Index++ with great care

This follows from the previous example but it deserves its own section for added stress. It may seem convenient to match a short pattern and then manually move the cursor to also manipulate another nearby section, but **do not use `c.Index++` / `c.Index--` outside of matched blocks**. Doing so is considered undefined behavior because you cannot guarantee where a mod has injected new instructions.

Manually moving the cursor **within** a matched block is, however, absolutely fine. Here is an example in `MinionLeashBodyBehavior.OnDisable` where the need for this arises.

```
if (SceneInfo.instance.sceneDef.cachedName == "meridian")
IL_0000: call class RoR2.SceneInfo RoR2.SceneInfo::get_instance()
IL_0005: callvirt instance class RoR2.SceneDef RoR2.SceneInfo::get_sceneDef()
IL_000a: callvirt instance string RoR2.SceneDef::get_cachedName()
IL_000f: ldstr "meridian"
IL_0014: call bool [netstandard]System.String::op_Equality(string, string)
IL_0019: brfalse.s IL_002c
```

Here we want to fix runtime NRE errors in the above line by changing it to `SceneInfo.instance?.sceneDef?.cachedName`.

```cs
ILLabel falseBranch = null;
// Matching such a long block allows us to put the cursor at the beginning
// of our instruction block while
// - ensuring that `SceneInfo.instance.sceneDef` exists, and
// - caching the brfalse target at the end which we need to use
if (c.TryGotoNext(
    x => x.MatchCallOrCallvirt(AccessTools.PropertyGetter(typeof(SceneInfo), nameof(SceneInfo.instance))),
    x => x.MatchCallOrCallvirt(AccessTools.PropertyGetter(typeof(SceneInfo), nameof(SceneInfo.sceneDef))),
    x => x.MatchCallOrCallvirt(AccessTools.PropertyGetter(typeof(SceneDef), nameof(SceneDef.cachedName))),
    x => x.MatchLdstr(out _),
    x => x.MatchCallOrCallvirt<string>("op_Equality"),
    x => x.MatchBrfalse(out falseBranch)))
{
    // This puts the cursor after `SceneInfo.instance` has been loaded.
    // We already know what this instruction is so there are no surprises.
    c.Index++;

    // Now emit the following instructions to null check this value
    // and skip the conditional block with the cached brfalse target.
    c.Emit(OpCodes.Dup);
    c.Emit<UnityEngine.Object>(OpCodes.Call, "op_Implicit");
    c.Emit(OpCodes.Brtrue, c.Next);
    c.Emit(OpCodes.Pop);
    c.Emit(OpCodes.Br, falseBranch);

    // As we emit instructions in front of the cursor, the
    // cursor also moves in front of the emitted instruction
    // just like a text cursor leaves behind a typed letter.
    // We are still after `SceneInfo.instance` and by advancing
    // the cursor one more to the right, we will be after the
    // `SceneInfo.instance.sceneDef` value has been loaded to
    // the stack. We then repeat the same process to null check
    // it as well. In fact, the previous logic could be put in
    // a for loop to avoid duplicating code.
}
```

### Emitting instructions

**Constants**

`Ldc_I4`, `Ldc_R4`, and `Ldstr` can be used to push integers, floats, and strings to the stack respectively. For example `c.Emit(OpCodes.Ldc_I4, 10);`

Booleans are treated as integers with the values 0 and 1.

**Arguments**

Methods are called with 0+ arguments. They are 0-indexed, so the first argument is `Ldarg_0`, the second `Ldarg_1`, etc. If the method is an instance method, the first argument is the instance. For example, for `HealthComponent.TakeDamageProcess(DamageInfo damageInfo)`, `Ldarg_0` is `this` and `Ldarg_1` is `damageInfo`. To load an argument on the stack

```cs
c.Emit(OpCodes.Ldarg_0);
// Ldarg_0 to Ldarg_3 are provided for convenience, for higher values use
c.Emit(OpCodes.Ldarg, 4);
```

**Fields**

A class instance field can be loaded on the stack with `Ldfld` as long as the class instance is already at the top of the stack.

```cs
// Assuming we're in TakeDamageProcess
c.Emit(OpCodes.Ldarg_0);
c.Emit<HealthComponent>(OpCodes.Ldfld, nameof(HealthComponent.body));
```

To store a value to an instance field, from the bottom to the top of the stack we need the class instance and the value we want to store.

```cs
// Still in TakeDamageProcess
c.Emit(OpCodes.Ldarg_0);
c.Emit(Ldc_R4, 100f);
c.Emit<HealthComponent>(OpCodes.Stfld, nameof(HealthComponent.health));
```

The equivalent for static fields `Ldsfld` and `Stsfld`. Note that if you want to access a static subtype, `c.Emit<Type.Subtype>()` is not allowed and you have to resort to `c.Emit(typeof(Type.Subtype))`.

```cs
// Loading `RoR2Content.Items.Hoof`
c.Emit(OpCodes.Ldsfld, typeof(RoR2Content.Items), nameof(RoR2Content.Items.Hoof));
```

**Local variables**

Loading and storing to local variables can be done with `Ldloc` and `Stloc`. For the latter the value you want to store needs to be at the top of the stack.

Local variables are identified by a 0-based index, e.g., `c.Emit(OpCodes.Ldloc, 5)`. Similar to loading arguments, `Ldloc_0` to `Ldloc_3` and the respective for `Stloc` are provided for convenience.

Hardcoding the variable index can lead to errors if the code is ever updated so it is advised to use [dynamic local variable indexing](#2-dynamically-match-the-index-of-a-local-variable) instead for future-proofing.

It is also possible to create your own local variables with the following code

```cs
// A new local variable of type string
VariableDefinition myVariable = new VariableDefinition(il.Import(typeof(string)));
il.Body.Variables.Add(myVariable);

// We can refer to it by its index
int varIndex = il.Body.Variables.Count - 1;
c.Emit(OpCodes.Ldstr, "Hello world");
c.Emit(OpCodes.Stloc, varIndex);
```

**Methods**

Before calling a method, its arguments from left to right must have been loaded to the stack in a bottom-to-top order. If the method is not static, its instance type (`this`) must also be loaded as the very first argument.

There are two call instructions, `Call` and `Callvirt`. The first is used when the method is static or it belongs to a struct since those always have a non-null instance. For every other case the latter should be used.

Static example

```cs
// Debug.Log("Hello world");
c.Emit(OpCodes.Ldstr, "Hello world");
c.Emit<Debug>(OpCodes.Call, nameof(Debug.Log));
```

Instance example

```cs
// We want to call `this.GetHealthAtRatio(0.25f)` from HealthComponent.TakeDamageProcess
c.Emit(OpCodes.Ldarg_0);         // push `this` on the stack
c.Emit(OpCodes.Ldc_R4, 0.25f);   // push the float argument on the stack
c.Emit<HealthComponent>(OpCodes.Callvirt, nameof(HealthComponent.GetHealthAtRatio));
```

If the method name has overloads, we need to specify exactly which one we want by using [Reflection](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Reflection-Crash-Course/).

```cs
// We want to add a buff to the attacked body in `TakeDamageProcess`
c.Emit(OpCodes.Ldarg_0);
c.Emit<HealthComponent(OpCodes.Ldfld, nameof(HealthComponent.body));
c.Emit(OpCodes.Ldsfld, typeof(RoR2Content.Buffs), nameof(RoR2Content.Buffs.Weak));
// CharacterBody.AddBuff has two overloads; one with BuffDef and one with BuffIndex.
// Specify which one we want by declaring the argument types in GetMethod.
MethodInfo method = AccessTools.Method(typeof(CharacterBody), nameof(CharacterBody.AddBuff), new Type[] { typeof(BuffDef) });
c.Emit(OpCodes.Callvirt, method);
```

**Properties**

Properties are simply methods under the hood with the "get_" and "set_" name prefix respectively. While `c.Emit<Type>(OpCodes.Callvirt, "get_" + nameof(Type.propertyName))` works, there are also other convenience methods available.

```cs
c.Emit(OpCodes.Ldarg_0); // push a CharacterBody instance on the stack
c.Emit(OpCodes.Callvirt, AccessTools.PropertyGetter(typeof(CharacterBody), nameof(CharacterBody.footPosition)));
```

#### Conditionals and branches

In CIL there is no concept of curly braces to specify what code belongs in an if-else block, instead the code flow is redirected with instruction jumps. `Brtrue` and `Brfalse` jump to the target instruction if the boolean at the top of the stack is true or false respectively, while `Br` jumps unconditionally.

There is also a family of conditional branches related to comparing two numbers, i.e., `Ble` ( _branch if **less** or **equal**_), `Blt` (_branch if **less than**_), `Bge`, `Bgt`, `Beq` (_branch if **equal**_), and `Bne` (_branch if **not equal**_).

It is simpler to understand how the instruction flow is affected by jumps with an example. Suppose we have the following code

```cs
public void Example(bool flag)
{
    if (flag)
    {
        this.DoTrue();
    }
    else
    {
        this.DoFalse();
    }
    this.DoAfter();
}
```

The respective CIL becomes

```
IL_0000: ldarg.1
IL_0001: brfalse.s IL_000b

IL_0003: ldarg.0
IL_0004: call instance void C::DoTrue()
IL_0009: br.s IL_0011

IL_000b: ldarg.0
IL_000c: call instance void C::DoFalse()

IL_0011: ldarg.0
IL_0012: call instance void C::DoAfter()
IL_0017: ret
```

We start by pushing the boolean to the stack with `IL_0000`. Then for the majority of the instructions, we see that `IL_0003`-`IL_0004` (if true), `IL_000b`-`IL_000c` (else), and `IL_0011`-`IL_0017` (after any if-else) are pretty much ordered serially. On the other hand `IL_0001 brfalse` does the opposite check than what our C# version does. The reason for this is that if `brfalse` is not satisfied, we naturally continue to `IL_0003`, which is the if-block. We also notice that `IL_0009 br` that comes after the end of the if-block serves the purpose of skipping any else-block instructions. On the other hand, at the end of the else-block we don't need such a jump because we naturally continue with the next instruction anyway. In a sense the instruction targets for `brfalse` and `br` specify which instructions belong to the else-block, while the positioning of `brfalse` and `br` themselves specify which instructions belong to the if-block.

If our conditional involves null checking a value, we may also see the presence of `dup` and `pop` which duplicate/pop the value at the top of the stack. For example, in `CharacterBody.OnInventoryChanged` at some point the method executes the following code:

```cs
// For clarity we should view the following line as
// if (this.onInventoryChanged != null)
// {
//     this.onInventoryChanged.Invoke();
// }
this.onInventoryChanged?.Invoke();
CharacterBody.onInventoryChangedGlobal?.Invoke(this);
```

When we look at the CIL for this we see

```
// this.onInventoryChanged?.Invoke();
IL_030d: ldarg.0
IL_030e: ldfld class [netstandard]System.Action RoR2.CharacterBody::onInventoryChanged
IL_0313: dup
IL_0314: brtrue.s IL_0319

// (no C# code)
IL_0316: pop
IL_0317: br.s IL_031e

IL_0319: callvirt instance void [netstandard]System.Action::Invoke()

// CharacterBody.onBodyInventoryChangedGlobal?.Invoke(this);
IL_031e: ldsfld class [netstandard]System.Action`1<class RoR2.CharacterBody> RoR2.CharacterBody::onBodyInventoryChangedGlobal
IL_0323: dup
IL_0324: brtrue.s IL_0329
```

`IL_030d` and `IL_030e` push to the stack the value we want to null check. `IL_0313: dup` duplicates this value so now we have two copies of it sitting on the stack. `IL_0314: brtrue` consumes the top copy for the null check and we have one more on the stack. If our condition is satisfied, we jump to `IL_0319` where we consume the second copy from the stack to call our method. Then execution naturally continues with the second line of code. However, if our condition is not satisfied, we want to jump straight to the second line of code, but since the remaining copy of `this.onInventoryChanged` at the top of the stack has no way to be consumed, we must pop it ourselves.

This `dup`-`pop` combo can be quite prevelant in cascading checks, e.g., `if (this.body && this.body.master && this.body.master.playerCharacterMasterController)`.

##### Example 1: Put some code in an if block

`RoR2.Mecanim.ClearLayerWeight.OnStateEnter` executes the following code unconditionally.

```
// animator.SetLayerWeight(layerIndex2, 0f);
IL_0026: ldarg.1
IL_0027: ldloc.0
IL_0028: ldc.r4 0.0
IL_002d: callvirt instance void [UnityEngine.AnimationModule]UnityEngine.Animator::SetLayerWeight(int32, float32)

IL_0032: ret
```

Generally whenever a layer index is involved in an animation, the code should check `if (layerIndex >= 0)` first to prevent the animator method from printing a warning to the console. To fix the above case ourselves, we need to find the instruction that comes after that method call, then put our cursor back to the beginning of the animator line and emit a conditional check. If our check is not satisfied, we jump to the next instruction we have cached.

```cs
var c = new ILCursor(il);
int localVarIndex = -1;
if (c.TryGotoNext(
	MoveType.After,
    x => x.MatchLdarg(1),
    x => x.MatchLdloc(out localVarIndex),
    x => x.MatchLdcR4(out _),
    x => x.MatchCallOrCallvirt<Animator>(nameof(Animator.SetLayerWeight))))
{
    Instruction nextInstr = c.Next;

    // Put our cursor back before the first matched instruction.
    // This is safe as long as we don't go further back than that.
	// If we wanted to surround multiple lines of code in the if block,
	// we'd have to use `c.TryGotoPrev` to go back to the first line since
	// here we only matched the last line to cache the next instruction.
    c.Index -= 4;

    c.Emit(OpCodes.Ldloc, localVarIndex);
    c.Emit(OpCodes.Ldc_I4_0);
    // We branch for the negation of our intended check:
    // NOT "greater or equal" is equivalent to "less than"
    c.Emit(OpCodes.Blt_S, nextInstr);
}
```

##### Example 2: Null check a statement to patch NRE errors

Assuming we have a method call or field access whose type instance could be null. This would be something like `variable.MyMethod()` and we'd want to convert it to `variable?.MyMethod()`. The original instructions would be something like

```
IL_0000 ldloc      
IL_0001 callvirt
IL_0002 somethingelse
```

In order to null check this, we need to insert the following instructions between `ldloc` and `callvirt` as explained in the [Conditionals and branches](#conditionals-and-branches) section.

```
dup
brtrue IL_0001
pop
br IL_0002
```

An example of this has already been covered [above](#4-use-cindex-with-great-care).

##### Example 3: Add an else to an if

Sometimes instead of modifying the code of an if-block, you want to extend it by adding an else-block for your own case. Let's assume we have the following code

```cs
public void Example(bool flag)
{
    if (flag)
    {
        this.DoTrue();
    }
    this.DoAfter();
}
```

where its CIL is

```cs
IL_0000: ldarg.1
IL_0001: brfalse.s IL_0009

IL_0003: ldarg.0
IL_0004: call instance void C::DoTrue()

IL_0009: ldarg.0
IL_000a: call instance void C::DoAfter()
IL_000f: ret
```

This is the same example from the [top of the section](#conditionals-and-branches) with missing the else-block. The steps of adding that are

1. Go between the last instruction of the if-block and the first instruction after
2. Add the `br` targetting the instruction after
3. Add the else-block code while caching the first instruction of this block
4. Go back to the `brfalse` (if check) and reassign the target to the first instruction of the else-block

In code this would be

```cs
// 1
if (!c.TryGotoNext(
    MoveType.After,
    x => x.MatchCallOrCallvirt<C>(nameof(C.DoTrue)))
{
    Log.Error("Patch failed");
    return;
}    

// 2
c.Emit(OpCodes.Br, c.Next);

// 3.1 Add the first instruction of the else-block and cache it
c.Emit(OpCodes.Ldarg_0);
Instruction elseInstruction = c.Prev;
// 3.2 Add the remaining instructions of the else-block
c.Emit<C>(OpCodes.Callvirt, nameof(C.DoFalse));

// 4
if (c.TryGotoPrev(x => x.MatchBrfalse(out _))
{
    c.Next.Operand = elseInstruction;
}
```

## Hook chain

Unlike On Hooks which are executed every time the hooked method is called, IL Hooks are executed only once when applied.

If multiple mods hook the same method, e.g., `A` -> `B` -> `C`, the hooks are applied in the same order. More specifically

* when `A` is registered, `A` is applied
* when `B` is then registered, we start from a clean state and apply `A` and then `B`
* etc

This also means that if some hook logs a message, we may observe it multiple times.

Similarly, if some hook makes bad assumptions and emits invalid code, an error will be logged to the console. If this error is logged multiple times, you need to look for the first instance of it to find the offending hook.

## Manual IL Hooks

Manually applying an IL Hook works exactly in the same way as for [On Hooks](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Hooking/On-Hook#manual-on-hooks).

The only differences are

* We use `new ILHook()` instead of `new Hook()`
* The `hookDelegate` always has the same (and simpler) signature of `void HookName(ILContext il)`
* The hook config is `ILHookConfig` instead of `HookConfig`

### Hooking a coroutine (IEnumerator)

Let's take `RoR2.Console.AwakeCoroutine` for example. When we view its IL instructions, we see

```
IL_0000: ldc.i4.0
IL_0001: newobj instance void RoR2.Console/'<AwakeCoroutine>d__57'::.ctor(int32)
IL_0006: dup
IL_0007: ldarg.0
IL_0008: stfld class RoR2.Console RoR2.Console/'<AwakeCoroutine>d__57'::'<>4__this'
IL_000d: ret
```

This does not correspond at all to the decompiled C#. This is because IEnumerators are treated as state machines. If we look at the members of `RoR2.Console`, we'll see a weird named class called `<AwakeCoroutine>d__57` which contains the functionality for advancing the state machine. We are mainly interested in its `MoveNext` method which actually contains the code we see for the decompiled C#. All the original `Console.AwakeCoroutine` does under the hood is set up the state machine as witnessed by its IL instructions.

So in order to modify the coroutine, we actually need to create an IL Hook for that special MoveNext method. We start by defining a helper method

```cs
private static ILHook CreateIEnumeratorILHook(Type type, string methodName, ILContext.Manipulator manipulator)
{
    var allFlags = (BindingFlags)(-1);
    var moveNext = type.
        GetNestedTypes(allFlags).
        FirstOrDefault(t => t.Name.Contains(methodName)).
        GetMethods(allFlags).
        FirstOrDefault(m => m.Name.Contains("MoveNext"));
    return new ILHook(moveNext, manipulator);
}
```

which can then be used like

```cs
CreateIEnumeratorILHook(typeof(RoR2.Console), nameof(RoR2.Console.AwakeCoroutine), hookDelegate);
```

MonoMod.Utils provides a very handy extension for [`MethodInfo`](https://github.com/MonoMod/MonoMod/blob/master/MonoMod.Utils/Extensions.Utils.cs#L237) which can also achieve the same thing.

```cs
MethodInfo targetMethod = AccessTools.Method(typeof(RoR2.Console), nameof(RoR2.Console.AwakeCoroutine));
new ILHook(targetMethod.GetStateMachineTarget(), hookDelegate);
```

## Misc notes

* Output the result IL of a delegate
  ```
  var dmd = new DynamicMethodDefinition(method.Method);
  var il = dmd.GetILProcessor().Body.Instructions.ToList();
  ```
  With thanks to Viseyth for finding the solution to this problem.
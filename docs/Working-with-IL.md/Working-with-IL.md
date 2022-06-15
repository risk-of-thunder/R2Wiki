## What is CIL?
In short, [CIL](https://en.wikipedia.org/wiki/Common_Intermediate_Language) is a stack based language that can manipulate arbitrary high level objects within a low level, stack based environment. CIL is a powerful tool which can provide access to restricted/protected areas of code, such as local variables within methods, private fields, private subtypes/objects, or allow the interception of readonly values. IL Hooks will allow you to manipulate method logic in ways that are not possible with On.Event hooks.

### What is a Stack?
A [Stack](https://en.wikipedia.org/wiki/Stack_(abstract_data_type)) is used at a low/intermediate level to process data. As explained by Wikipedia, only the value at the very top of the stack can be popped from the stack. Given a list of IL instructions, the processor will iterate its way through the operations, emitting values to the stack, and executing operations upon the stack as and when the instruction is executed. At the point of termination(return), the stack must be empty, with no more instructions or values remaining. If this is not the case, the stack is said to be unbalanced and the process will terminate with the error `Invalid ILCode`

    ldc.i4.1 //pushes the int32(1) onto the stack
    ldc.i4.2 //pushes the int32(2) onto the stack
    ldc.i4.3 //pushes the int32(3) onto the stack
    add
    add
    ret //method return

In the above example, after the first 3 instructions, from bottom to top the stack contains `[1,2,3]`. When the first add operation is executed, first [3] and then [2] will be popped from the stack, added together to produce [5], which is then pushed onto the stack resulting in `[1,5]`. Finally, the second add will be executed, where [5] and [1] will be popped from the stack, added together, and the resulting stack will contain only `[6]`. The method will then return, passing the value 6 to the calling method. The method has finished executing, there are no more operations and the stack is now empty, thus the stack is balanced. If the stack is not balanced, or you perform an incorrect operation on the data at the top of the stack, the `ILCode` error will be produced.

You can find out about specific [OpCodes](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.add?view=netframework-4.8) from MSDN. Each opcode will explain how many parameters it pops from the stack, the ordering of the values, and the return type of the operation.

### ILCursor & basic stack manipulation
The IL Cursor is an implementation by [MonoMod](https://github.com/MonoMod/MonoMod) to facilitate traversal of the stack in a user friendly manner. `ILCursor` is a specific implementation by monomod, so you will not find any MSDN documentation on it.

To begin, we will define a problem to solve. Lets say that we desire to modify the health fraction of the crowbar. Firstly, we must understand that damage is handled by `HealthComponent.TakeDamage`, and all damage interactions are serverside. TakeDamage instantly returns if run by a client.

		public void TakeDamage(DamageInfo damageInfo)
		{
			if (!NetworkServer.active)
			{
				Debug.LogWarning("[Server] function 'System.Void RoR2.HealthComponent::TakeDamage(RoR2.DamageInfo)' called on client");
				return;
			}
Similarly, the relevant section of code from HealthComponent.TakeDamage() is:

	if (this.combinedHealth >= this.fullCombinedHealth * 0.9f)
	{
		int itemCount2 = master2.inventory.GetItemCount(ItemIndex.Crowbar);
		if (itemCount2 > 0)
		{
			Util.PlaySound("Play_item_proc_crowbar", base.gameObject);
			num *= 1.5f + 0.3f * (float)(itemCount2 - 1);
			EffectManager.instance.SimpleImpactEffect(Resources.Load<GameObject>("Prefabs/Effects/ImpactEffects/ImpactCrowbar"), damageInfo.position, -damageInfo.force, true);
		}
	}

Clearly, we want to modify the 0.9f. If we right click the line with DnSpy, and edit IL, we can see the corresponding opcodes for this line:

    175	01F1	ldarg.0
    176	01F2	call	instance float32 RoR2.HealthComponent::get_combinedHealth()
    177	01F7	ldarg.0
    178	01F8	call	instance float32 RoR2.HealthComponent::get_fullCombinedHealth()
    179	01FD	ldc.r4	0.9
    180	0202	mul
    181	0203	blt.un.s	216 (0266) ldarg.0 

So at operation index 179 we want to replace that value, but how do we get there? Well, we could simply move the cursor to the 179'th instruction, however if another mod, or another update modifies any instruction prior to this, the modification will likely break the game. Therefore, we are going to move the cursor to some specific instructions that we match directly. The `ILCursor` will assist with this task, and we will require the following references:

    using Mono.Cecil.Cil;
    using MonoMod.Cil;

Inside of Awake() we are going to define our hook location and an `ILCursor`. We will then use lambda chaining & the monomod match methods to find specific instructions. **The ILCursor's location is always placed between one instruction, and the next. Debug.Log(c) will output the two instructions either side of the cursor.** The ILCursor location will bind before the first instruction that is matched, in this case the cursor will be ready to execute `Ldarg.0`. In order to reach our destination, we need to increment the cursor index by 4 to place ourselves before `Ldc.R4(0.9f)`. A common exception you will see is the `KeyNotFoundException` which will occur if one or more of your match operations does not hold true. It is likely that the operand specified for the match is incorrect, or in the case of `MatchCall`, you will likely want to use `MatchCallVirt`.

            IL.RoR2.HealthComponent.TakeDamage += (il) =>
            {
                ILCursor c = new ILCursor(il);
                    c.GotoNext(
                        x => x.MatchLdarg(0),
                        x => x.MatchCallvirt<HealthComponent>("get_combinedHealth"),
                        x => x.MatchLdarg(0),
                        x => x.MatchCallvirt<HealthComponent>("get_fullCombinedHealth"),
                        x => x.MatchLdcR4(0.9f)
                        );
                    c.Index += 4;
            }

In this example, lets say we just want to increase this to 80% health instead of 90%. We can simply:

    c.Next.Operand = 0.8f;

### Delegates

Alternatively, perhaps we want a bit more complicated logic, but we don't want to go writing a ton of different opcodes, logic, and branches in order to accomplish the task. In such a scenario we can use a `Delegate`, which is a method that we can define via a lambda expression. For example instead of the above remove and emit, we could use something like the following:

	c.Remove();
	c.Emit(OpCodes.Ldloc_1);
	c.EmitDelegate<Func<CharacterBody, float>>((cb) =>
	{
		if (cb.master.inventory)
		{
			int bars = cb.master.inventory.GetItemCount(ItemIndex.Crowbar);
			if (bars > 0)
			{
				return 1f - ((1f - 1f / (_crowbarScalar * (float)bars + 1f)) * _crowbarCap);
			}
		}
		return 0.9f;
	});

`Delegates` can either return a value, as defined by `Func<T>`, or they can return void, defined by `Action<T>`. In the Case of `Func<T>` the last type defined is the return type, in the above example the return type is a float. The delegate also takes a `CharacterBody` as a parameter, which is referred too by the arguement `cb`. 

> EmitDelegate was designed to behave the same as call / callvirt, thus passing all args from stack onto the delegate just like if it was a normal method call
> thus, if you push A, B, C, your delegate should accept A, B, C in that order. It doesn't pop one by one, it "pops" the last N elements from the stack at once.
https://discordapp.com/channels/562704639141740588/562704639569428506/604753431894294528

 At the cursor location there is currently no reference of a `CharacterBody` ontop of the stack, so if we want to refer to this object we need to put one there. The method body of `TakeDamage` defines the local Variable `characterBody` on line 380 of `HealthComponent`, so a reference to the attackers characterBody already exists within the method. As such, we can simply emit this local variable to the stack using `c.Emit(OpCodes.LdLoc1);`. We have removed the existing value, pushed the character body onto the stack, ready to be popped by our delegate which then performs its own method logic before returning a value which is then pushed back onto the stack ready to be manipulated by the original code/logic.

**Output the result IL of a delegate**

    var dmd = new DynamicMethodDefinition(method.Method);
    var il = dmd.GetILProcessor().Body.Instructions.ToList();

With thanks to Viseyth for finding the solution to this problem.

### Arguments, Properties, Fields, Local Variables & Values, Private, readonly, SubTypes

**Arguements**

Methods are called with 0+ arguments, and we can refer to those arguments by the use of `c.Emit(OpCodes.Ldarg0);`, `c.Emit(OpCodes.Ldarg1);`, `c.Emit(OpCodes.Ldarg2);` and so on. Ldarg0 is a reference to the current object/(this). Ldarg1,2,3 would be the parameters passed to the method. For instance `(this).TakeDamage(Ldarg1,Ldarg2...)`

If we want to manipulate the object (this) via a delegate, we can simply emit Ldarg0 to the stack, and then call a delegate method which takes a parameter using this.Type.

**Properties**
Properties are called like methods. For example if we want to return the this.fullHealth Property of the HealthComponent: 

    c.Emit(Opcodes.Ldarg0) //push this/HealthComponent onto the stack.
    c.Emit(OpCodes.Call, typeof(HealthComponent).GetMethod("get_fullHealth")); //call the get_fullHealth method of (this)

**Fields**
We can load a field reference onto the stack somewhat similarly by specifying the TypeInfo and calling the GetFieldCached method:

    c.Emit(OpCodes.Ldarg0);
    c.Emit(OpCodes.Ldfld, typeof(HealthComponent).GetFieldCached("health");

First (this) is pushed to the stack, and then we load the field "health" from the TypeInfo defined by HealthComponent, which consumes (this) from the stack, and pushes the value of this.health field onto the stack. Similarly, if we want to store a value inside a field, we can use the OpCode Stfld, which will consume the value ontop of the stack and store it as the value of the field reference.

**Local Variables**
Local Variables are manipulated with ldloc.# to load and stloc.# to store. Local variables are named incrementally with numbers, much like arguments are named. Local variables always refer to the current scope, and as such there is no need to emit ldarg.0 to the stack, as the variables only apply to this object anyway.

    c.Emit(OpCodes.Ldloc_1);
    c.Emit(OpCodes.Ldloc_2);
    c.Emit(OpCodes.Add);
    c.Emit(OpCodes.Stloc_s, (byte)3);

The above would load the value of the first local variable onto the stack, then load the value of the second local variable onto the stack, add would then pop both values from the stack, and then push the result onto the stack. Stloc(2) would then pop the result from the stack, and store it in the third local variable.

**Values**

We can push values directly onto the stack, ready to be manipulated or store in other fields by emitting primitives such as integers and floats.

    c.Emit(OpCodes.Ldc_I4, 1); //will push (int32)1 onto the stack.
    c.Emit(OpCodes.Ldc_R4, 2f); //will push (float32)2f onto the stack.
    c.Emit(OpCodes.Stloc_s, (byte)3); //will pop 2f from the stack and store it in the third local variable.
    c.Emit(OpCodes.Stloc_s, (byte)4); //will pop 1 from the stack and store it in the fourth local variable.

**Private fields**

There are two methods we can use to refer to a private field. First, we can specify BindingFlags as a parameter:

	c.Emit(OpCodes.Ldarg_0); 
	c.Emit(OpCodes.Ldfld, typeof(HealthComponent).GetField("increaseHealingCount", BindingFlags.Instance | BindingFlags.NonPublic)); 

Secondly, we can use the GetFieldCached method, which will not only be faster for any future references, but will also increase readability:

	c.Emit(OpCodes.Ldarg_0); 
	c.Emit(OpCodes.Ldfld, typeof(HealthComponent).GetFieldCached("increaseHealingCount"));

**Readonly**
please put some stuff here kthxbye

**Subtypes**

Subtypes can be referenced by using the GetNestedType/Cached method.

                c.Emit(OpCodes.Ldarg_0);
                c.Emit(OpCodes.Ldfld, typeof(HealthComponent).GetNestedType("RepeatHealComponent", BindingFlags.Instance | BindingFlags.NonPublic).GetFieldCached("reserve")); //private field (this)HealthComponent/RepeatHealComponent.reserve
                c.Emit(OpCodes.Ldfld, typeof(HealthComponent).GetNestedTypeCached("RepeatHealComponent").GetFieldCached("reserve")); //cached reference to private field (this)HealthComponent/RepeatHealComponent.reserve

### Conditionals, Branches, Iteration, & Labels/Return addresses

todo

### Returning the calling method from the delegate

todo

### Remove() & jump addresses

It is important to note, that removing instructions can result in IndexOutOfBounds exception when an already existing instruction references the address of an instruction which has been removed. In such a scenario its best to use `cursor.Next.OpCode`/`.Operand` to replace the instruction with either the desired operation, branch, or NUL.

todo


## Original:

IL Instructions and what they do - https://en.wikipedia.org/wiki/List_of_CIL_instructions  
If this is you're first time, I highly suggest reading this [impromptu tutorial](https://discordapp.com/channels/562704639141740588/562704639569428506/569809963845943311) from 0x0ade  

To get started, make sure your project is referencing MonoMod.Utils.dll and Mono.Cecil.dll  
We can view C# translated to IL with dnSpy. Right click on what you want to edit and press `Edit IL Instructions`

### Example code for removing the strange sensitivity while sprinting  
What we want to edit is at line 413 of RoR2.CameraRigController
```
IL.RoR2.CameraRigController.Update += (il) => {
    var c = new ILCursor(il);

    //We use GotoNext to locate the code we want to edit
    //Notice we can specify a block of instructions to match, rather than only a single instruction
    //This is preferable as it is less likely to break something else because of an update

    c.GotoNext(
        x => x.MatchLdloc(4),      // num14 *= 0.5f;
        x => x.MatchLdcR4(0.5f),   // 
        x => x.MatchMul(),         // 
        x => x.MatchStloc(4),      // 
        x => x.MatchLdloc(5),      // num15 *= 0.5f;
        x => x.MatchLdcR4(0.5f),   //
        x => x.MatchMul(),         //
        x => x.MatchStloc(5));     //

    //When we GotoNext, the cursor is before the first instruction we match.
    //So we remove the next 8 instructions
    c.RemoveRange(8);
                
};
```

### Example code snippet from Bepis  
```

        public void Awake()
        {
            IL.RoR2.CombatDirector.AttemptSpawnOnTarget += il =>
            {
                var cursor = new ILCursor(il);

                cursor.GotoNext(x => x.MatchStloc(8));


                //at this point, the CharacterMaster component is already on the stack
                //we need to pass in the current instance onto the stack too, so we call `ldarg 0`, which is `this`

                cursor.Emit(OpCodes.Ldarg_0);

                cursor.EmitDelegate<Func<CharacterMaster, CombatDirector, CharacterMaster>>((component, director) =>
                {
                    CharacterMaster characterMaster = component;
                    StartCoroutine(GiveRandomItems(characterMaster));
                    Chat.AddMessage("Ran EmitDelegate, Component is : " + component.ToString());
                    return characterMaster;
                });
            };

        }  
```

Tips: 
* Use `Debug.Log(il.ToString())` to see what your edit has done.  
* 
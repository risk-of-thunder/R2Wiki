# Console Commands

## Getting started

The console is a hidden in-game window in which you can view game logs and you can type developer commands. In this tutorial we will learn how to write our own commands.

The key combination to open the console is different depending on your keyboard layout. Generally the combination is `Ctrl + Alt` plus the button that is underneath the `Esc` key. In the case of a US/UK keyboard that is the grave key.

Shortcut (please add shortcuts for your region if necessary):  
* UK/US = Ctrl + Alt + \`
* DE = Ctrl + Alt + Ö
* ES = Ctrl + Alt + Ñ
* RU = Ctrl + Alt + Ё

Many mods use console commands. Notably mods like [DebugToolkit](https://thunderstore.io/package/Harb/DebugToolkit/) are almost entirely console commands.

To enter a command first you must open the console, then type in the text box at the bottom of the window. The console has intellisense so as you start typing a command the console will make suggestions as to which command you want to type. You can start typing then use the up/down arrow keys to select the command you want from the list.

Sometimes commands require [arguments](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/passing-parameters). If a command requires arguments you can add them after the command separated by a space.

So command follows this general format:

`mycommandname args[0] args[1] args[2] ... args[n]`

e.g. if we have a command called `give_item` which takes 3 arguments in order args[0]=partial itemname, args[1]=count, args[2]=playerIndex (this is a real example from DebugToolkit, simplified for sake of tutorial)

`give_item behemoth 10 0` will give 10 of brilliant behemoth to player at index 0 (this is usually the host).

## CommandHelper

*Also consider reading [R2API's page](https://github.com/risk-of-thunder/R2API/wiki/CommandHelper) about this.*

One thing that will make your life much easier is [R2API's CommandHelper](https://github.com/risk-of-thunder/R2API/blob/master/R2API/Utils/CommandHelper.cs) and it basically does all the command registering for you.

### How to use Commandhelper for your commands

Logically, it would make more sense to put this at the end of the tutorial after writing the commands but this is just a small bit that we can cover very quickly but it is also important not to miss.

Before you start your plugin's class, add: `[R2APISubmoduleDependency(nameof(CommandHelper))]`. This is right next to your BepInEx attribute. See also [Using Submodules](https://github.com/risk-of-thunder/R2Wiki/wiki/Using-R2API's-Submodules)

In your main mod `public void Awake()` method you just need to add this little snippet which tells R2API to scan your mod for console commands:

```csharp
public void Awake()
{
    ...
    R2API.Utils.CommandHelper.AddToConsoleWhenReady();
    ...
}
```

Done. Now we can forget about this and write as many commands as we like.

## Writing your first command

To make out command we need to make a private static method with a ConCommand attribute. The method also has to take ConCommandArgs as an argument. See below:

```csharp
[ConCommand(commandName = "MyCommandName", flags = None, helpText = "Help text goes here")]
private static void MyCommandName(ConCommandArgs args)
{
    //Actual code here 
}
```

Simple enough so far. Just replace the name of the method, the commandName and the helpText and you are ready to write your code.

Every command take `ConCommandArgs` as an argument. You can simply think of this as an array of strings you will receive from the console command (just like in the example above).

Although every command needs to have this `ConCommandArgs`, not every command needs to use it.

### Example 1: no argument command

Some commands don't need an argument. Imagine a command which you just want to see a list of all player names, just type `listallplayers` into the console with no arguments.

```csharp
[ConCommand(commandName = "listallplayers", flags = ConVarFlags.ExecuteOnServer, helpText = "Lists all players in game")]
private static void ListAllPlayers(ConCommandArgs args)
{
    var userNames = NetworkUser.readOnlyInstancesList.Select(u => u.userName);
    foreach(var user in userNames)
    {
        Debug.Log(user);
    }
}
```

Notice how this command never uses `args`. This command will read the list of users, select the username and then write their username to the console window. 

### Example 2: using arguments

Now imagine a command in which we need to set a value of something. Lets just say for example we want to set the jump height of survivors to a certain int value.

```csharp
[ConCommand(commandName = "jump_set", flags = ConVarFlags.ExecuteOnServer, helpText = "Set jump height of survivor. args\[0\]=(int)value")]
private static void JumpSet(ConCommandArgs args)
{
    int jumpHeight = args.GetArgInt(0);
    SetSurvivorJumpHeight(jumpHeight);     //Imagine some other method somewhere else in your mod does this.
}
```

In this case, we use an argument that can be accessed from args. If we type `jump_set 10` for example then `args[0] = 10`. the ConCommandArgs struct contains methods for parsing this to an int so that we can pass it to a method that will set the value for us.

Do note at this point that this is fine when we are typing in expected values but what happens if someone types in the wrong arguments? This is where error catchment is a good practice.

## Error catchment

There are reams of information on what to do with error catchment. This is a small dive into that.

For more information, a good place to start is [here](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/exceptions/)

If we look at the above example 2 what happens when someone types in a wrong argument? E.g. `jump_set horse`. We get an error. This isn't a problem as it will be handled by the game logic but wouldn't it be nicer if we could handle it ourselves and give a meaningful message to the user?

There are many ways to go about this and we'll dive into two of them:
* The easy and dirty thing to do here is to surround your command code in a `try{}catch{}` block. If you haven't written your command you can create this block by using a build in VS snippet by typing `try` then pressing `Tab` twice. Alternatively, if you have already written your code you can highlight the code you want to surround then press `Ctrl + K + S` then type `try` and press `Enter` when you have try selected. (For more useful shortcuts see [here](https://docs.microsoft.com/en-us/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio?view=vs-2019)) 
```csharp
[ConCommand(commandName = "jump_set", flags = ConVarFlags.ExecuteOnServer, helpText = "Set jump height of survivor. args\[0\]=(int)value")]
private static void JumpSet(ConCommandArgs args)
{
    try
    {
        int jumpHeight = args.GetArgInt(0);
        SetSurvivorJumpHeight(jumpHeight);     //Imagine some other method somewhere else in your mod does this.
    }
    catch(Exception ex)
    {
        Debug.LogError(ex);
    }
}
/* Now when there is an error instead of throwing an error up it will be caught and it will instead run the code within the catch block.
 In the example above it writes the exception to the console,
 but instead of doing this you could write your own message here.
 Note if you do write your own message you miss out on making custom exceptions,
 but you might not care about this. */
```

* The more elegant way, is to use a tryParse. Luckily, the args struct already provides these:
```csharp
[ConCommand(commandName = "jump_set", flags = ConVarFlags.ExecuteOnServer, helpText = "Set jump height of survivor. args\[0\]=(int)value")]
private static void JumpSet(ConCommandArgs args)
{
    int? jumpHeight = args.TryGetArgInt(0);
    if (jumpHeight.HasValue)
    {
        SetSurvivorJumpHeight(jumpHeight); //Imagine some other method somewhere else in your mod does this.
    }
    else
    {
        Debug.LogError("Couldn't parse the jump height!");
    }
}
```

You can check the argument count by using `args.Count`, or use `args.CheckArgumentCount(X)` to check if the command was called with at least X arguments.

## Making arguments user-friendly

As a general rule: users are sloppy and lazy! Don't take offense at that, it is just a good rule to work with.

* String arguments. Try and make these case-insensitive when possible.
* Bool arguments. Users will use `0`, `false`, `-1` to denote false and `1`, `true` to denote true. Consider using the following code snippet to parse your bools, as the default ConCommandArgs struct only provides a parser for `true` and `false`. 
```csharp
/// <summary>
/// Try to parse a bool that's either formatted as "true"/"false" or a whole number "0","1". Values above 0 are considered "truthy" and values equal or lower than zero are considered "false".
/// </summary>
/// <param name="input">the string to parse</param>
/// <param name="result">the result if parsing was correct.</param>
/// <returns>True if the string was parsed correctly. False otherwise</returns>
internal static bool TryParseBool(string input, out bool result)
{
    if (bool.TryParse(input, out result))
    {
        return true;
    }

    if (int.TryParse(input, out int val))
    {
        result = val > 0 ? true : false;
        return true;
    }

    return false;
}
```
* Float arguments. Some countries use a `,` instead of the American `.` (`1,5` versus `1.5`). Try and check if the argument was parsed correctly before inputting it raw into your code.


## Naming convention

There is no agreed-upon naming convention for console command names within the Risk of Rain 2 modding community. The name of your command must be unique and make sense within the context of what the command is doing.

A recommendation is to avoid conflicts is to use the initials of your mod, followed by an underscore followed by a descriptive name of the command.


# Configuration

## Getting Started

Often when making a mod you will have variables which you may want to change based on user preference.
Such variables will also be able to be live edited if you add support for hot reloading. 

e.g. In a mod which gives a player money at the start of each stage it would be good to be able to set how much money you think you should receive each stage.

This can easily be achieved by using configuration. We can automatically generate a configuration file by accessing the `Config` field of `BaseUnityPlugin`.

```csharp
public abstract class BaseUnityPlugin : MonoBehaviour
    {
        protected BaseUnityPlugin();

        protected ManualLogSource Logger { get; }
        protected ConfigFile Config { get; } //This is the field we are interested in!
    }
```

All mods that you create will [inherit](https://docs.microsoft.com/en-us/dotnet/csharp/tutorials/inheritance) from `BaseUnityPlugin` so we can do our configuration from there!

If you want your config file to have a custom name to it you can make a new ConfigFile instance and give it the path that will contains the custom name, like this for example :

`private static ConfigFile CustomConfigFile { get; set; }`
Then initialize it : 

`CustomConfigFile = new ConfigFile(Paths.ConfigPath + "\\CustomNamedFile.cfg", true);`

The second parameter, if set to true, will create the file even if no ConfigWrapper are made.
If you want to create a ConfigFile on a custom folder, refer to the final section of this guide.

Before we do this though we should add a `ConfigEntry<T>`. This class can be found in the namespace `Bepinex.Configuration` and we use this as our main data holder for our configuration values. So we can start off by adding one of these to our project. ConfigEntries are always `public static ...` and they are always fields in a class inheriting from BaseUnityPlugin (as already mentioned above).

`public static ConfigEntry<int> MyConfigEntry { get; set; }`  

*NOTE: `T` is a [generic type](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/). When we create a class with a `<T>` we tell Visual Studio what type we want this class to use. In this case we are using `int`.*

Currently, BepInEx supports the following Types for Configuration:
* string
* bool
* byte
* sbyte
* short
* ushort
* int
* uint
* long
* ulong
* float
* double
* decimal
* enum
* Color
* Vector2
* Vector3
* Vector4
* Quaternion

Now we have the ConfigEntry set up we must use the BaseUnityPlugin `Config.Bind<T>()` method in order to give our ConfigEntry values and tell the mod what to put in the configuration file.

Config.Bind<T>() has a couple of overloads but the second one I find most useful. You will need:
```csharp
string SectionName, //the name of the section of the configuration file this configuration will be stored (useful if you have multiple configurations)
string Key, 		//this is the name of what you are going to be looking up in configuration
T DefaultValue,  	//default value for your configuration
string Description	//optional field which you can add description of what this configuration does and what to put in it
```

So in our case:

```csharp
[BepInDependency("com.bepis.r2api")]
[BepInPlugin("com.Tutorials.ConfigurationTutorial", "ConfigurationTutorial", "1.0.0")]
public class ConfigurationTutorial : BaseUnityPlugin
{
	public static ConfigEntry<int> MyConfigEntry { get; set; }
	
	public void Awake()
	{
		MyConfigEntry = Config.Bind<int>(
		"MySection",
		"MyKey",
                10,
		"This is the description"
		)
	}
}
```
If you use a custom named config file, be sure to .Bind on the actual instance we made !

Now your configuration is set up, lastly what you need to do to get the value out of the ConfigEntry is call `MyConfigEntry.Value` at the place in your code where you need the value.

## Generating the configuration file

This is the easy bit. It is all done for you! Just build your mod and put it in `Bepinex\Plugins` as usual. After the first time you run the game you will get a configuration file in `Bepinex\config` with the name of your plugin.

To edit the values you simply open the file in your favourite text editor, change the values and save the file. The next time you run the game your mod will use the new values.

Configuration files will look something like this:

```
[MySection]

# This is the description.
MyKey = 10

```

## Adding multiple configurations

You need to have a unique ConfigEntry and call `Config.Bind` on each one for every configuration value your mod needs to use.

```csharp
[BepInDependency("com.bepis.r2api")]
[BepInPlugin("com.Tutorials.ConfigurationTutorial", "ConfigurationTutorial", "1.0.0")]
public class ConfigurationTutorial : BaseUnityPlugin
{
	public static ConfigEntry<int> MyConfigEntry { get; set; }
	public static ConfigEntry<int> MySecondConfigEntry { get; set; }

	public void Awake()
	{
		MyConfigEntry = Config.Bind<int>(
		"MySection",
		"MyKey",
                10,
		"This is the description"
	        )

		MySecondConfigEntry = Config.Bind<int>(
		"MySection",
		"MySecondKey",
                5,
		"This is the description of the second thing"
		)
	}
}
```

This will give a configuration file which looks something like this:

```
[MySection]

# This is the description.
MyKey = 10

# This is the description of the second thing.
MySecondKey = 5

```

## Hot Reloading

If you want to see the change made to the config file into the game without having to restart it, you can call the Reload() Method available in the BepinEx ConfigFile class.
Here is a quick example with a console command :
```
[ConCommand(commandName = "mod_reload", flags = ConVarFlags.None, helpText = "Reload the config file of the mod.")]
private static void CCReloadConfig(ConCommandArgs args)
{
	ConfigFile.Reload();
}

Additionally `ConfigEntry`s have an event that triggers when they are changed, this can be used to get fine control over your mod at runtime.

```

## A worked example

The following example will build on top of [In depth First mod](https://github.com/risk-of-thunder/R2Wiki/wiki/%5BIn-depth%5D-First-mod)

After finishing that tutorial you should have code which looks similar to below:

```csharp
    namespace ConfigurationTutorial
    {
		[BepInDependency("com.bepis.r2api")]
		[BepInPlugin("com.Tutorials.ConfigurationTutorial", "ConfigurationTutorial", "1.0.0")]
		public class ConfigurationTutorial : BaseUnityPlugin
		{
			public void Awake()
			{
				On.EntityStates.Huntress.ArrowRain.OnEnter += (orig, self) =>
				{
					Chat.AddMessage("This is our message!");
					orig(self);
				}
			}
		}
    }
```

1) Make sure your project is set up correctly. All references are resolved, all necessary using statements are already entered at top of file. If you have any red underlines, as always, you can click on them then press `Ctrl + .` to see if Visual Studio can find a resolution to the error.

2) Add ConfigEntry field to your mod.

```csharp
    namespace ConfigurationTutorial
    {
		[BepInDependency("com.bepis.r2api")]
		[BepInPlugin("com.Tutorials.ConfigurationTutorial", "ConfigurationTutorial", "1.0.0")]
		public class ConfigurationTutorial : BaseUnityPlugin
		{
			public static ConfigEntry<string> HuntressRainMessage { get; set; }
		
			public void Awake()
			{
				On.EntityStates.Huntress.ArrowRain.OnEnter += (orig, self) =>
				{
					Chat.AddMessage("This is our message!");
					orig(self);
				}
			}
		}
    }
```

3) Call `Config.Bind<T>()` in awake method and assign value to your ConfigWrapper

```csharp
    namespace ConfigurationTutorial
    {
		[BepInDependency("com.bepis.r2api")]
		[BepInPlugin("com.Tutorials.ConfigurationTutorial", "ConfigurationTutorial", "1.0.0")]
		public class ConfigurationTutorial : BaseUnityPlugin
		{
			public static ConfigEntry<string> HuntressRainMessage { get; set; }
		
			public void Awake()
			{
				HuntressRainMessage = Config.Bind<string>(
					"Huntress",
					"ArrowRainMessage",
					"This is our message!",
					"The message which is played when ArrowRain.OnEnter is called"

				)
			
				On.EntityStates.Huntress.ArrowRain.OnEnter += (orig, self) =>
				{
					Chat.AddMessage("This is our message!");
					orig(self);
				}
			}
		}
    }
```

4) Now change in your hook methods where you want your Configuration value to be called.

```csharp
    namespace ConfigurationTutorial
    {
		[BepInDependency("com.bepis.r2api")]
		[BepInPlugin("com.Tutorials.ConfigurationTutorial", "ConfigurationTutorial", "1.0.0")]
		public class ConfigurationTutorial : BaseUnityPlugin
		{
			public static ConfigEntry<string> HuntressRainMessage { get; set; }
		
			public void Awake()
			{
				HuntressRainMessage = Config.Bind<string>(
					"Huntress",
					"ArrowRainMessage",
					"This is our message!",
					"The message which is played when ArrowRain.OnEnter is called"

				)
			
				On.EntityStates.Huntress.ArrowRain.OnEnter += (orig, self) =>
				{
					Chat.AddMessage(HuntressRainMessage.Value);
					orig(self);
				}
			}
		}
    }
```

5) We are all done with the code now! Build your mod and add it to the plugins directory in Bepinex folder as per usual. After the first time you run the game you will get a configuration file appear in `\Bepinex\Configuration` which will be named after your mod (in this case `com.Tutorials.ConfigurationTutorial.cfg`)

Open the configuration file in your favourite text editor and you can edit the value.

*NOTE: any changes you make to this file while the game is running is not guaranteed to make changes in the game. You should usually close the game before making any changes. For on-the-go configuration changes then I recommend looking at tutorial for [Console Commands](https://github.com/risk-of-thunder/R2Wiki/wiki/Console-Commands).*

## Config folders

In case you need to create multiple configuration files, it may be a good idea to create a Folder for storing your config files, you can use System.IO's directory class for creating the directory for your configuration files.

Here's an example of a class that has a method that creates 2 config files, and a folder to store them.
	
	using BepInEx;
	using BepInEx.Configuration;
	using System.IO;
	
	public static class ConfigManager
	{
		//Declare config files
		private static ConfigFile configFileA;
		private static ConfigFile configFileB;
		
		//Nameof the folder where we'll store our config files.
		private const string configFolderName = "MyConfigFolder";
		//A property that gets the absolute path of our config folder
		//Paths is a static class from BepInEx that contains useful, absolute paths.
		private static string ConfigFolderPath { get => Path.Combine(Paths.ConfigPath, configFolderName); }

	    private static void Init()
	    {
		// Ensures that the config folder exists, if not, create it.
		if (!Directory.Exists(ConfigFolderPath))
                {
                    Directory.CreateDirectory(ConfigFolderPath);
                }
		//Create the config files, notice how we use Path.Combine for creating the new files.            
                configFileA= new ConfigFile(Path.Combine(ConfigFolderPath, "ConfigFileA.CFG"), true);
                configFileB = new ConfigFile(Path.Combine(ConfigFolderPath, "ConfigFileB.CFG"), true);
	    }
	}
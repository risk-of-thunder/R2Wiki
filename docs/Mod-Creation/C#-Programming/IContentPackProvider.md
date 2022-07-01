# IContentPackProvider

This guide provides how to Create, Load and Manage content via RoR2's ContentPackProvider system.

## Q&A Section

### ContentPack Q&A

Q: What's a ContentPack?
A: A ContentPack is a class in the RoR2.ContentManagment namespace, it basically represents a Package of contents for Risk of Rain 2 to Load and Manage via it's catalog systems.

Q: What goes in a ContentPack?
A: Most pieces of content that are managed by a catalog are supposed to be added via ContentPacks, Examples of these are:
* ItemDef, EquipmentDef, BuffDef, EffectDef, etc
* Body Prefabs
* Master Prefabs
* Projectile Prefabs

Q: Are there any precautions i should take?
A: The main precaution you need is naming your assets, RoR2 expects each asset in a contentPack to have a unique name. Alongside this, you cannot have any duplicate assets (Having two itemDefs that are identical will crash the game)

Q: Ok, how do i load my ContentPack to the game?
A: You load content packs by inheriting from IContentPackProvider interface, information on this interface can be seen below.

### SerializableContentPack Q&A

Q: What's a SerializableContentPack?
A: A SerializableContentPack, as the name suggests, its a Serializable version of a ContentPack, it's a ScriptableObject in the RoR2 namespace that allows for the serialization of contentPacks via the Editor. While SerializableContentPack is not updated for the new fields of ContentPack added in SurvivorsOfTheVoid, anyone is free to inherit from SerializableContentPack and create an updated version, Regardless, R2API comes with an updated version called (SOTVSerializableContentPack)

Q: Are there any benefits to using this over a normal content pack?
A: There are a few benefits, if youre working on a code only aproach, it is best to simply use a normal ContentPack, If your mod is done thru Thunderkit, then you can have your ContentPack pre built by the SerializableContentPack.

Q: Any precautions i should take?
A: There are 2 Precautions
* Just like in ContentPack, all the content assets should both have unique names, and not have duplicates.
* You cannot have "Missing" assets in the arrays, this causes a crash.

Q: How do i load my SerializableContentPack to the game?
A: SerializableContentPack has a method called "CreateContentPack", whcih as the name suggests, creates a content pack based of the fields you add to the SCP. You can then load the created ContentPack via IContentPackProvider

### IContentPackProvider Q&A

Q: What's IContentPackProvider
A: IContentPackProvider (Simply known as ContentPackProvider), is an interface that RoR2 uses to identify what classes are the classes that load ContentPacks into the game.

Q: Are there any benefits to using this over just adding the ContentPack to R2API's ContentManager?
A: While it is true that R2API now can handle the loading of any content pack you give, Implementing your own Provider may come in handy if you want to have more control over how your content loads.

Q: Any precautions i should take?
A: None really, While IContentPackProvider may seem scary due to being asynchronous, it isnt specifically a difficult to comprehend interface.

Q: How can i use IContentPackProvider to load my mod's Content?
A: check the section called "Using IContentPackProvider"

## Using ContentPack, SerializableContentPack & IContentPackProvider

### Using ContentPack
The ContentPack class is mainly a Code only class, the collection of contents are handled via a NamedAssetCollection.
The NamedAssetCollection's main method for adding content is .Add(), it accepts an IEnumerable of the Type of content you want to add. It is reccommended that you have already a prebuilt list of whatever content you wish to add before you start adding Content to the ContentPack.

If your mod's setup requires each contentPiece to be added separately (Either because each content piece is a class or for other reasons), it is reccommended that you use SerializableContentPack instead.

The main Property you want to set is "Identifier". Identifier is an unique identifier for your contentPack, it is reccommended that you set this identifier to your mod's GUID, or mod's Name.

### Using SerializableContentPack

As stated in the Q&A for SCP, this is mainly an editor enviroment scriptable object, but you can also use it in code.

The serializable content pack looks something like this in the editor, Each array corresponds to a type of content to add.

![](https://cdn.discordapp.com/attachments/575431803523956746/946571393577910312/2fda574822b213cd8b602582741446b6.png)

Some things of note:
A Body prefab is just a prefab that has a CharacterBody component, projectiles with body components need to be added here alongside the Projectile Prefabs array.
Master Prefabs are components with CharacterMasters
ProjectilePrefabs are components with PRojectileControllers
GameModePrefabs are components with "Run" components
NetworkedObjectPrefabs are components that have NetworkIdentities, but do not have any of the other components listed above. (example of these are NetworkedBodyAttachments, Interactables, etc)

To create the ContentPack from the SerializableContentPack, you can use the "CreateContentPack" method.

### Using IContentPackProvider

The interface contains 4 members, these are:
* Identifier: This should be a unique identifier for your provider, you can set it to be the same identifier as your content pack, or viceversa.
* LoadStaticContentAsync: The first method to be called in the ContentPackProvider system, LoadStaticContentAsync can be used for a variety of things, but their main use is for Creating/Loading content assets, and Loading them to your ContentPack or SerializableContentPack, you can see how RoR2 does this in their RoR2Content class
* GenerateContentPackAsync: The Second method to be called in the CotnentPackProvider system, GenerateContentPackAsync is where you Copy the contents of your ContentPack to the Finalized content pack generated by RoR2. Extreme caution must be taken with this method, as it is called multiple times per instance of the interface.
* FinalizeAsync: The final method to be called in the ContentPackProvider system, it is called when the Finalized content pack is created. this method gets called moments before the Catalogs get their destined content pieces and begin populating themselves.

### How to handle the Async:

#### Simple:
You can simply add these two lines at the bottom of each method to properly return the values ror2 expects. This report progress makes it so the percentage number in the loading screen increases as the game loads our content. of course, this is the simplest way of doing things.

	args.ReportProgress(1f);  
	yield break;


#### Complex:
You can use the async functionality to have the game properly show how much more time it takes before all content is loaded, A great example on how to do this is by looking at how RoR2Content loads content.

```csharp
	public IEnumerator LoadStaticContentAsync(LoadStaticContentAsyncArgs args)
	{
		contentPack.identifier = identifier;  
		ContentLoadHelper contentLoadHelper = new ContentLoadHelper();  
		contentLoadHelper.assetRepository = Resources.Load<ResourcesDirectory>("ResourcesDirectory");
		Action[] loadDispatchers = new Action[foo]
		{
			delegate
			{
				Foo.Bar();
			}
			delegate
			{
				Foo2.Bar()
			}
			//etc etc...
		}
		int j = 0;  
		while (j < loadDispatchers2.Length)  
		{  
			loadDispatchers2[j]();  
			args.ReportProgress(Util.Remap(j + 1, 0f, loadDispatchers2.Length, 0f, 0.05f));  
			yield return null;  
			int num = j + 1;  
			j = num;  
		}
	}
```

This piece of code showcases how we remap the progress data to match how many actions it takes for us to load the content, RoR2 uses this system only on LoadStaticContentAsync, the other 2 methods they use the simple method.

#### Example IContentPackProvider class using only ContentPack

```csharp
	//Inherit interface
	public class MyModContent : IContentPackProvider
	{
		internal ContentPack contentPack = new ContentPack();
		//Your unique identifier, setting this to your mod's name or mod's GUID is recommended.
		public string identifier => MainClass.GUID;
		
		private static void Init()
		{
			ContentManager.collectContentPackProviders += (addContentPackProvider) => {
				addContentPackProvider(new MyModContent());
			};
		}

		private static void AddContentPack(ContentManager.AddContentPackProviderDelegate addContentPackProvider)
		{
			addContentPackProvider(Singleton);
		}
		
		public IEnumerator LoadStaticContentAsync(LoadStaticContentAsyncArgs args)
		{
			//Write or call methods to load content to your ContentPack
			args.ReportProgress(1f);
			yield break;
		}
		//This is the simplest way of implementing this method, only add more stuff if you know what you're doing.
		public IEnumerator GenerateContentPackAsync(GetContentPackAsyncArgs args)
		{
			contentPack.identifier = identifier;
			//This is a static method
			ContentPack.Copy(contentPack, args.output);
			args.ReportProgress(1f);
			yield break;
		}
		public IEnumerator FinalizeAsync(FinalizeAsyncArgs args)
		{
			args.ReportProgress(1f);
			yield break;
		}
	}
```

#### Example IContentPackProvider class using a SerializableContentPack

```csharp
	public class MyModContent : IContentPackProvider
	{
		private ContentPack contentPack;
		internal SerializableContentPack serializableContentPack;
		public string identifier => MainClass.GUID;
		
		private static void Init()
		{
			//Load your serializable content pack from your assetbundle, or create it directly, Commented out so you implement it
			serializableContentPack = //Assets.MainAssetBundle.LoadAsset<SerializableContentPack>("ContentPack");
			ContentManager.collectContentPackProviders += (addContentPackProvider) => {
				addContentPackProvider(new MyModContent());
			};
		}
		
		public IEnumerator LoadStaticContentAsync(LoadStaticContentAsyncArgs args)
		{
			//Write or call methods to load the rest of your content to your SerializableContentPack
			//Once all the content is loaded in the SerializableContentPack,  Create the ContentPack with the method.
			contentPack = serializableContentPack.CreateContentPack();
			args.ReportProgress(1f);
			yield break;
		}
		//This is the simplest way of implementing this method, only add more stuff if you know what you're doing.
		public IEnumerator GenerateContentPackAsync(GetContentPackAsyncArgs args)
		{
			contentPack.identifier = identifier;
			//This is a static method
			ContentPack.Copy(contentPack, args.output);
			args.ReportProgress(1f);
			yield break;
		}
		public IEnumerator FinalizeAsync(FinalizeAsyncArgs args)
		{
			args.ReportProgress(1f);
			yield break;
		}
	}
```

### ProTip: Load Content using Reflection, LinQ and AssetBundle methods.
If you have all your content in AssetBundles, you can easily load all the content of type T by doing assetBundle.LoadAllAssetsOfTypeT and use reflection.

```csharp
	//Loads all EffectPrefabs from an assetBundle and retunrs an array ready to be added to a ContentPack.
	public GameObject[] LoadAllEffects(AssetBundle bundle)
	{
		return bundle.LoadAllAssetsOfType<GameObject>()
			.Where(go => go.GetComponent<EffectDef>());
	}
```

You can also use this to load things like EntityStates to your ContentPack.

```csharp
	//Finds all EntityStates from your assembly and returns an array ready to be added to a ContentPack
	public SerializableEntityStateType[] GetEntityStateTypes()
	{
		return GetType().Assembly.GetTypes()
				.Where(type => typeof(EntityStates.EntityState).IsAssignableFrom(type))
				.Where(type => !type.IsAbstract())
				.Select(type => new SerializableEntityStateType(type))
				.ToArray();
	}
```
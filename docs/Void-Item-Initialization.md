# How to Setup Void Item Transformations

First things first, this wiki is expecting you to already know how to make your own custom items in the game. If you do not, go check out the other tutorials on the wiki as well as KomradeSpectre's video tutorial.

This wiki will cover initializing the transformations for the void item you have created. This process remains the same whether you are managing your project from visual studio or creating items in thunderkit.

## VoidItemAPI

VoidItemAPI is a tool I have setup to easily create transformations for your item. You will not have to set the expansion def or do any hooking yourself. The only downside of using it is that it's yet another dependency you will have to set for your project and you will have to wait on me during updates. If you are interested, you can get the dll from thunderstore or from the github repo [here](https://github.com/khouryj/VoidItemAPI).

## Manual Initialization

The sanest way of creating void items is to do it yourself and not rely on my garbage code to do it for you. So hopefully my explanation skills are greater than my programming skills.

### Setting the Item Tier

The first thing you want to do is make sure your ItemDef tier is set to one of the void tiers. You can technically have void transformations on non-void tiered items, but I don't see the point in doing that.

**In thunderkit:**

<img width="327" alt="image" src="https://user-images.githubusercontent.com/31513176/163300558-49e6ffc0-11f1-4e13-aa20-efffc49e22e0.png">

**or In Project:**

<img width="461" alt="image" src="https://user-images.githubusercontent.com/31513176/163301131-e1bbb32f-103f-4763-9c5d-c46133115f96.png">

### Setting the ExpansionDef

All void items should have their expansion def set to SurvivorsOfTheVoid. This solution needs to be done in code, and this line of code needs to be run after the ExpansionCatalog has been initialized, otherwise you will get a null reference exception. I just do this later when I hook onto the event to initialize the void transformations, however you can hook onto the initialization of ExpansionCatalog if you want to for this step.

To set the expansion def, you can do this:

```c#
YourItemDef.requiredExpansion = ExpansionCatalog.expansionDefs.FirstorDefault(def => def.nameToken == "DLC1_NAME")
```

### Creating the transformation(s)

The first thing of note is that you need to be hooked onto ContagiousItemManager.Init, this will not work unless your code runs in this method and before their original method. If you are using mmhook to generate this hook, orig() needs to be called after your code runs. You can hook onto the event like this:
```c#
//In Awake
On.RoR2.Items.ContagiousItemManager.Init += MethodNameHere;

//Outside of Awake
private void MethodNameHere(On.RoR2.Items.ContagiousItemManager.orig_Init orig)
{
    
}
```

To create a transformation for your item, you are going to need some things. For the sake of example, let's just say I have a void item named Blue Whip, and I wanted to create a transformation that corrupts Red Whips (surprising). To do this, I need to create something called an ItemDef.Pair, like so:

```c#
private void MethodNameHere(On.RoR2.Items.ContagiousItemManager.orig_Init orig)
{
    ItemDef.Pair transformation = new ItemDef.Pair()
    {
        itemDef1 = RoR2Content.Items.SprintOutOfCombat,  //Item to be transformed
        itemDef2 = BlueWhip                              //The void item
    };

}
```

Once you have created your ItemDef.Pair, all you need to do is add it to the dictionary in the ItemCatalog. There is a method in HarmonyLib that does this easily for you. Also, do not forget to call orig()!

```c#
private void MethodNameHere(On.RoR2.Items.ContagiousItemManager.orig_Init orig)
{
    ItemDef.Pair transformation = new ItemDef.Pair()
    {
        itemDef1 = RoR2Content.Items.SprintOutOfCombat,  //Item to be transformed
        itemDef2 = BlueWhip                              //The void item
    };
    ItemCatalog.itemRelationships[DLC1Content.ItemRelationshipTypes.ContagiousItem] = ItemCatalog.itemRelationships[DLC1Content.ItemRelationshipTypes.ContagiousItem].AddToArray(transformation);
    orig();
}
```

With this setup, you have successfully initialized the transformation of your void item! Note: if you would like to setup multiple transformations for one void item, you can simply set up an array of ItemDef.Pair objects, and then use the `AddRangeToArray()` method to add your array to the dictionary in the ItemCatalog.
# Void Items

First things first, this wiki is expecting you to already know how to make your own custom items in the game. If you do not, go check out the other tutorials on the wiki as well as KomradeSpectre's video tutorial.

This wiki will cover initializing the transformations for the void item you have created. This process remains the same whether you are managing your project from visual studio or creating items in thunderkit.

## Manual Initialization

### Setting the Item Tier

The first thing you want to do is make sure your ItemDef tier is set to one of the void tiers. You can technically have void transformations on non-void tiered items, but I don't see the point in doing that.

**In thunderkit:**

<img width="327" alt="image" src="https://user-images.githubusercontent.com/31513176/163300558-49e6ffc0-11f1-4e13-aa20-efffc49e22e0.png">

**or In Project:**

<img width="461" alt="image" src="https://user-images.githubusercontent.com/31513176/163301131-e1bbb32f-103f-4763-9c5d-c46133115f96.png">

### Setting the ExpansionDef

All void items should have their expansion def set to SurvivorsOfTheVoid. To set the expansion def, you can do this:

```c#
YourItemDef.requiredExpansion = Addressable.LoadAssetAsync<ExpansionDef>("RoR2/DLC1/Common/DLC1.asset").WaitForCompletion();
```

## Creating the transformation(s)

### ItemRelationshipProvider

This is the scriptable object that will get added to your ContentPack. Simply create a new ItemRelationshipProvider and set the relationship type to the DLC1 contagious item type.

```c#
yourProvider.relationshipType = Addressables.LoadAssetAsync<ItemRelationshipType>("RoR2/DLC1/Common/ContagiousItem.asset").WaitForCompletion();
```
You then want to populate the `ItemRelationshipProvider.relationships` array with the transformation pairs that you want to add. These `ItemDef.Pair` structs can contain custom or vanilla items, but neither of the items can be null! Keep in mind that using vanilla items requires you to load the addressable ItemDef object. Using `RoR2.RoR2Content.Items` or any similar static field **WILL NOT WORK!!**

The addressables can be found here https://xiaoxiao921.github.io/GithubActionCacheTest/assetPathsDump.html

![{D967968C-E50C-42AF-98FA-2A554585E290}](https://github.com/user-attachments/assets/b43fa930-2409-40a6-9923-b7e50b6b9111)

You can either manage your own ContentPack and add it to the `ContentPack.itemRelationshipProviders` array or use `R2API.ContentAddition.AddItemRelationshipProvider`.

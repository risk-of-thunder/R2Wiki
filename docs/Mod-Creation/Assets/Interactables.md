# Interactables

## Step-by-Step Tutorial
* Have your mod project set up, check the [First Mod template](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Getting-Started/First-Mod/) if you haven't already. We'll be making a Beetle Memorial statue as the example interactable, the repo with the complete code is [here](https://github.com/FocusedFault/BeebleMemorial).
* Load the required assets, if you're using an existing game asset, you'll need to use PrefabAPI to clone the object so you don't affect the original. The below code includes code for both, choose one "beebleStatue". Note: If you use your own model, you'll need to register it using PrefabAPI after setting it up, `PrefabAPI.RegisterNetworkPrefab(InteractableModel);`

```csharp
private GameObject beebleStatue = Addressables.LoadAssetAsync<GameObject>("RoR2/Base/Beetle/mdlBeetle.fbx").WaitForCompletion();
private GameObject beebleStatue = PrefabAPI.InstantiateClone(Addressables.LoadAssetAsync<GameObject>("RoR2/Base/Beetle/mdlBeetle.fbx").WaitForCompletion(), "BeebleMemorialStatue");
private Material beebleStatueMat = Addressables.LoadAssetAsync<Material>("RoR2/Base/MonstersOnShrineUse/matMonstersOnShrineUse.mat").WaitForCompletion();
```

* Now that we have the assets, let's prep the beetle model to be turned into a shrine.

```csharp
// Changing the name to what you'd like it to be called
beebleStatue.name = "BeebleMemorialStatue"; 

// If the GameObject you're using doesn't have a NetworkIdentity, add one for multiplayer compat
beebleStatue.AddComponent<NetworkIdentity>(); 

// Scaling the model up 3x since we want a nice, large shrine
beebleStatue.transform.localScale = new Vector3(3f, 3f, 3f); 

// This applies the statue material to the mesh
beebleStatue.transform.GetChild(1).GetComponent<SkinnedMeshRenderer>().sharedMaterial = beebleStatueMat; 

// Adding a collider so the statue is solid, though it uses a SkinnedMeshRenderer so it doesn't easily work with a MeshCollider, for simplicity sake we're using a simple BoxCollider
beebleStatue.transform.GetChild(1).gameObject.AddComponent<BoxCollider>(); 
```

* After the asset has been prepped, now we'll need to turn it into an interactable

```csharp
// Add main necessary component PurchaseInteraction, this will add a Highlight component as well
PurchaseInteraction interaction = beebleStatue.AddComponent<PurchaseInteraction>(); 

// What the shrine displays when near
interaction.contextToken = "Beeble Memorial (E to pay respects)"; 

// What the shrine displays on ping
interaction.NetworkdisplayNameToken = "Beeble Memorial (E to pay respects)"; 

// The renderer that will be highlighted by our Highlight component
beebleStatue.GetComponent<Highlight>().targetRenderer = beebleStatue.GetComponentInChildren<SkinnedMeshRenderer>(); 

// Create a new GameObject that'll act as the trigger
GameObject trigger = Instantiate(new GameObject("Trigger"), beebleStatue.transform);

// Adding a BoxCollider and setting it to be a trigger so it's not solid 
trigger.AddComponent<BoxCollider>().isTrigger = true; 

// EntityLocator is necessary for the interactable highlight
trigger.AddComponent<EntityLocator>().entity = beebleStatue; 
```

* Great, we now have an interactable, but it does nothing at the moment, so let's create a component that adds functionality

```csharp
    public class BeebleMemorialManager : NetworkBehaviour
    {
      public PurchaseInteraction purchaseInteraction;
      private GameObject shrineUseEffect = Addressables.LoadAssetAsync<GameObject>("RoR2/Base/Common/VFX/ShrineUseEffect.prefab").WaitForCompletion();

      public void Start()
      {
        if (NetworkServer.active && Run.instance)
        {
          purchaseInteraction.SetAvailable(true);
        }

        purchaseInteraction.onPurchase.AddListener(OnPurchase);
      }

      [Server]
      public void OnPurchase(Interactor interactor)
      {
        if (!NetworkServer.active)
        {
          Debug.LogWarning("[Server] function 'BeebleMemorialManager::OnPurchase(RoR2.Interactor)' called on client");
          return;
        }

        EffectManager.SpawnEffect(shrineUseEffect, new EffectData()
        {
          origin = gameObject.transform.position,
          rotation = Quaternion.identity,
          scale = 3f,
          color = Color.cyan
        }, true);
        Chat.SendBroadcastChat(new Chat.SimpleChatMessage() { baseToken = "<style=cEvent><color=#307FFF>o7 to the Beebles we lost in the great lunar war.</color></style>" });
      }
    }
```

* Now we'll need to go back and add this component to the shrine and also add the purchaseinteraction to our new component

```csharp
BeebleMemorialManager mgr = beebleStatue.AddComponent<BeebleMemorialManager>();
PurchaseInteraction interaction = beebleStatue.AddComponent<PurchaseInteraction>();
interaction.contextToken = "Beeble Memorial (E to pay respects)";
interaction.NetworkdisplayNameToken = "Beeble Memorial (E to pay respects)";
mgr.purchaseInteraction = interaction;
```

* So the interactable is now done, you can spawn it directly wherever you want if that's what you're going for, though if you want it to be a normal interactable selectable by the stage director, you'll need to register it using DirectorAPI. First we'll need to create an InteractableSpawnCard that'll contain information that the director will need to spawn it.

```csharp
InteractableSpawnCard interactableSpawnCard = ScriptableObject.CreateInstance<InteractableSpawnCard>();
interactableSpawnCard.name = "iscBeebleMemorialStatue";
interactableSpawnCard.prefab = beebleStatue;
interactableSpawnCard.sendOverNetwork = true;
// The size of the interactable, there's Human, Golem, and BeetleQueen
interactableSpawnCard.hullSize = HullClassification.Golem; 
// Which nodegraph should it spawn on, air or ground
interactableSpawnCard.nodeGraphType = RoR2.Navigation.MapNodeGroup.GraphType.Ground; 
interactableSpawnCard.requiredFlags = RoR2.Navigation.NodeFlags.None;
// Nodes have flags that help define what can be spawned on it, any node marked "NoShrineSpawn" shouldn't spawn our shrine on it
interactableSpawnCard.forbiddenFlags = RoR2.Navigation.NodeFlags.NoShrineSpawn;
// How much should it cost the director to spawn your interactable
interactableSpawnCard.directorCreditCost = 0;
interactableSpawnCard.occupyPosition = true;
interactableSpawnCard.orientToFloor = false;
interactableSpawnCard.skipSpawnWhenSacrificeArtifactEnabled = false;
```

* Now we'll need to create the DirectorCard and the DirectorCardHolder

```csharp
DirectorCard directorCard = new DirectorCard
{
  selectionWeight = 100, // The higher this number the more common it'll be, for reference a normal chest is about 230
  spawnCard = interactableSpawnCard,
};

DirectorAPI.DirectorCardHolder directorCardHolder = new DirectorAPI.DirectorCardHolder
{
  Card = directorCard,
  InteractableCategory = DirectorAPI.InteractableCategory.Shrines
};
```

* Final step, we need to register it with the DirectorAPI, you can register it with all stages or specific stages

```csharp
// Registers the interactable on every stage
DirectorAPI.Helpers.AddNewInteractable(directorCardHolder);
// Or create your stage list and register it on each of those stages
List<DirectorAPI.Stage> stageList = new List<DirectorAPI.Stage>();

stageList.Add(DirectorAPI.Stage.DistantRoost);
stageList.Add(DirectorAPI.Stage.AbyssalDepthsSimulacrum);

foreach (DirectorAPI.Stage stage in stageList)
{
  DirectorAPI.Helpers.AddNewInteractableToStage(directorCardHolder, stage);
}
```

* Congrats! You can now pay respect to the beetles who bravely gave their lives in the Great Lunar War

![image](https://github.com/user-attachments/assets/4e6fb494-fe7c-4f60-8139-07a0c5b7ae83)
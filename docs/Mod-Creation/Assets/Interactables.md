# Creating an Interactable

## Quickstart
* Have your mod project set up, check the [First Mod template](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Getting-Started/First-Mod/) if you haven't already. We'll be making a Beetle Memorial statue as the example interactable.
* Load the required assets, if you're using an existing game asset, you'll need to use PrefabAPI to clone the object so you don't affect the original. The below code includes code for both, choose one.
```
private GameObject beebleStatue = Addressables.LoadAssetAsync<GameObject>("RoR2/Base/Beetle/mdlBeetle.fbx").WaitForCompletion();
private GameObject beebleStatue = PrefabAPI.InstantiateClone(Addressables.LoadAssetAsync<GameObject>("RoR2/Base/Beetle/mdlBeetle.fbx").WaitForCompletion(), "BeebleMemorialStatue");
private Material beebleStatueMat = Addressables.LoadAssetAsync<Material>("RoR2/Base/MonstersOnShrineUse/matMonstersOnShrineUse.mat").WaitForCompletion();
private GameObject shrineUseEffect = Addressables.LoadAssetAsync<GameObject>("RoR2/Base/Common/VFX/ShrineUseEffect.prefab").WaitForCompletion();
```
* Now that we have the assets, let's prep the beetle model to be turned into a shrine.
```
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
```
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
* Great, we now have an interactable, but it does nothing at the moment, so let's create a hook that adds functionality
```
On.RoR2.PurchaseInteraction.OnInteractionBegin += PurchaseInteraction_OnInteractionBegin; // The hook in the Awake() function

private void PurchaseInteraction_OnInteractionBegin(On.RoR2.PurchaseInteraction.orig_OnInteractionBegin orig, PurchaseInteraction self, Interactor activator)
{
   // Checks the name of the purchase interaction gameobject if it our shrine
   if (self.gameObject.name == "BeebleMemorialStatue(Clone)")
   {
     // Spawns a simple shrine activation effect
     EffectManager.SpawnEffect(shrineUseEffect, new EffectData()
     {
       origin = self.transform.position,
       rotation = Quaternion.identity,
       scale = 3f,
       color = Color.cyan
     }, true);

     // Send a chat message to everyone paying our respects
     Chat.SendBroadcastChat(new Chat.SimpleChatMessage() { baseToken = "<style=cEvent><color=#307FFF>o7 to the Beebles we lost in the Great Lunar War.</color></style>" });
   }
     else orig(self, activator); // This is so we don't break other purchase interactions
}
```
* Final step, we need a home for our beetle memorial, let's go with the Bazaar
```
On.RoR2.BazaarController.Start += BazaarController_Start; // The hook in the Awake() function

private void BazaarController_Start(On.RoR2.BazaarController.orig_Start orig, BazaarController self)
{
  orig(self);
  // Instance our shrine at the defined position
  GameObject statue = GameObject.Instantiate(beebleStatue, new Vector3(-109, -27, -40), Quaternion.identity); 
  NetworkServer.Spawn(statue);
}
```
* Congrats! You can now pay respect to the beetles who bravely gave their lives in the Great Lunar War
![beeble](https://cdn.discordapp.com/attachments/562704639569428506/1190750474702442596/image.png)
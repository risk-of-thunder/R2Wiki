# Creating an Interactable

## Quickstart
* survivorPod is an imported GameObject from the game `GameObject survivorPod = Addressables.LoadAssetAsync<GameObject>("RoR2/Base/SurvivorPod/SurvivorPod.prefab").WaitForCompletion()` though if you're using the actual survivor pod you'll need to remove some other components first but this is just for an example.
* You'll need 3 main components, PurchaseInteraction, Highlight, and EntityLocator. The PurchaseInteraction contains attributes that allow you to change the price, display string, and others of the interaction. The Highlight just highlights the meshrenderer you provide. The EntityLocator is needed for referencing the main GameObject since this will be attached to an empty GameObject with a BoxCollider. 
* The below code is a VERY basic example of what you'll need to do to make an object an interactable.

```
// Adding a PurchaseInteraction automatically adds some of the necessary components, like Highlight
survivorPod.AddComponent<PurchaseInteraction>();
// Get the MeshRenderer of the GameObject/Mesh you'd like highlighted
survivorPod.GetComponent<Highlight>().targetRenderer = survivorPod.transform.GetChild(0).GetChild(0).GetChild(1).GetComponent<MeshRenderer>();

GameObject something = new GameObject();

// This GameObject will act as the "trigger" that activates the purchaseinteraction
GameObject trigger = Instantiate(something, survivorPod.transform);
trigger.AddComponent<BoxCollider>();
trigger.AddComponent<EntityLocator>().entity = survivorPod;
```

##### Note: might add more to this, for now feel free to add any more info you think would be necessary
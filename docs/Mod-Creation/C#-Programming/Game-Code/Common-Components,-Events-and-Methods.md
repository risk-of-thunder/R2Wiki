# Common Components, Events and Methods

## RoR2
The RoR2 Namespace contains all game specific objects & references. For the most part, everything you might need to refer too, modify, or call is either here, or within the EntityStates namespace. EntityStates contains all the class definitions for any game object. Survivors, chests, AI/Monsters, and their respective behaviours is all defined within EntityStates.

RoR2 on the other hand contains most of the core game functionality, behaviours, components, and their containers.

## Useful Objects & methods

* `RoR2.Run.instance` - Returns the current Run of a game of RoR2. A Run is a session/playthrough of RoR2. RoR2.Run.runStartGlobal is a popular hook location for the start of a game. RoR2.Run contains many properties and methods, like the difficult coefficient, droplists, RNG, Boss Spawns, and time.
* `RoR2.SceneDirector` - The SceneDirector manages the logic & behaviour for each stage. The scene director controls AI spawning, Combat groups, Interactable spawns, player spawns, credits/RNG/Object placement.
* `RoR2.SpawnCard` - SpawnCards are used as metadata to control the different spawn types for both interactables as well as AI. Each Type is associate with a card that has a weight, cost, and category. Categories weights are summed to create a % chance for a specific item in a category to be spawned.
* `RoR2.CharacterBody` - Every Player/AI has a CharacterBody object. CharacterBody contains properties such as NetworkID's, Steam usernames, prefabs, Buffs, levels, statistics, equipment, and various effects/actions that Characters can perform. Every entity has a CharacterBody.
* `RoR2.HealthComponent` - HealthComponent contains all the health/shield/overhealth/healthreserve information. Every entity has a HealthComponent associated with them.
* `RoR2.NetworkUser` - NetworkUser can be used to reference the connected networkusers by means of readOnlyInstancesList. Each NetworkUser has a netID, which uniquely identifies that user. netIDs are regenerated every stage. NetworkUser.Network_id however is again unique, but remains unchanging throughout the run.
* `PlayerCharacterMasterController`- A reference specifically to Player controllers. PlayerCharacterMasterController.instances[i] can be used to refer to specific players.
* `LocalUserManager` - a reference specifically to local users. Use LocalUserManager.GetFirstLocalUser() to return the localhost/client.
* `GenericInteraction` - Class which handles just about every type of interaction of the player in the game (default press 'E' something). Hook into methods here to change the way you interact with objects.
* `GenericSkill` - Class which handles stats and information about skills. Can be used to edit existing skills or create your own new skills. See wiki page [Custom Skill Creation](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Skill-Creation) for more information on creating your own custom skills.
* `SurvivorDef` - Definition of a survivor (playable character). See the wiki page [Custom Character Creation](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Character-Creation) for more information on how to add your own survivors.
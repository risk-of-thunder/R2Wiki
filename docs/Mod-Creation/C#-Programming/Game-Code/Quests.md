# Quests
The Quest system is a System introduced in Hallowed Concepts that works as a rudimentary Quest framework, it's used for the Compulsion Shrine and it's Quests.

This page will go thru what are Quests, how they're similar (but different) to EntityStates, and how to create new Quests.

## What are Quests

Quests are **C#** classes that inherit from ``BaseQuest``, they have a similar framework to EntityStates with methods like ``OnEnter``, ``OnExit``, ``Update``, ``FixedUpdate``, etc. These quests have networking capabilities and as such have a Catalog.

Quests also have built in functionality with the Objective Panel. Allowing for the creation and coupling of Objectives with the Quest.

Quests have the capability of _dirtying_ themselves, causing a network update from Server to Clients

### Key Classes

* **BaseQuest**: Base Class for all Quests, all quests inherit this. BaseQuests are _executed_ by QuestRunners
* **QuestRunner**: A NetworkBehaviour that executes Quests, the networking happens here.
* **QuestCatalog**: Catalog for Quests, Quests can be queried by asking for a _base type_. This way a collection of quests of a specific type can be queried.
* **RegisterQuestAttribute**: Attribute for registering Quests to the ``QuestCatalog``. They inherit from ``SearchableAttribute``. This attribute has the necessary metadata to specify Quest Weight, an Identifier and the name of a method to check if the Quest can start under the current run context
* **QuestStarter**: A MonoBehaviour that _starts a quest_ on a ``QuestRunner``. The Quest is serialized in a ``SerializableSystemType`` field.

A concept on Quests is a ``QuestTracker``, QuestTrackers are _sibling_ MonoBehaviours from the main QuestRunner and have the responsibility of:
1. Starting new Quests
2. Handling Quest Completion (Wether succesful completion or failure)

Quests from the Shrine of Compulsion are tracked using the ``CompulsionQuestTracker``

## Similarities with EntityStates

Quests have framework similarities between EntityStates.

| EntityState Type | Quest Type |
|--|--|
| EntityState | BaseQuest |
| EntityStateMachine | QuestRunner |
| NetworkStateMachine | Quest Runner |
| EntityStateCatalog | QuestCatalog |

However, Quests are not EntityStates for the following reasons:

1. Quests don't have serialization support
2. QuestRunners are not meant to go to new quests directly, they're meant to execute a quest ad-infinitum until it's completion (or failure)
3. Quests are able to _dirty themselves_ and update across the network. On an EntityState you'd have to go to a separate EntityState to do the networking, and then go back to the original state.
4. Quests are _Server Driven_, while EntityStates are _Authority Driven_

## Creating a new Quest

We'll create a very basic new Compulsion Quest where the player has to survive for 10 seconds, but only if the current stage is _Titanic Plains_

```
namespace Quests.CompulsionQuests
{
  //To register a quest we need to decorate the class with the RegisterQuest attribute. Make sure the identifier is unique.
  //We'll specify the "canStartMethodName" so we can ensure the quest has a 50% chance to start
  //If no "canStartMethodName" is specified, then the quest can always start.
  [RegisterQuest(nameof(CompulsionQuestSuriveTenSeconds), canStartMethodName = nameof(CanStart))]
  public class CompulsionQuestSurviveTenSeconds : BaseCompulsionQuest
  {
    //The CanStart method needs to be static, return a boolean, and have a single argument, which is of type QuestRunner. This method is called on the Server
    //The QuestRunner component can be used to retrieve information to determine if the Quest can begin. Since we can use it to reach for sibling components or other data structures.
    //Keep in mind that a quest _never starts_ if this method returns false.
    private static bool CanStart(QuestRunner questRunner)
    {
      return Util.CheckRoll(50);
    }

    private const int SECONDS_TARGET = 10f;

    //The timer will run on the server
    private float _timerServer;

    //This is the _timerServer, rounded to an int and synced across machines.
    private int _timerSync;

    public override void FixedUpdate(float fixedDeltaTime)
    {
      //Much like EntityStates, we always want to call the base methods.
      base.FixedUpdate(fixedDeltaTime);

      if(NetworkServer.active && !isQuestOver)
      {
        _timerServer += fixedDeltaTime;
        if(_timerServer >= SECONDS_TARGET) //We've reached our time goal, mark the quest as completed.
        {
          EndQuest(completedSuccesfully: true);
        }

        //We need to sync the timer with the clients, if the timerServer rounded down is not equal to the sync variable, update the sync variable and mark dirty
        int num = Mathf.FloorToInt(_timerServer);
        if(num != _timerSync)
        {
          _timerSync = num;
          MarkDirtyServer(); //This will cause a network update
        }
      }
    }

    public override void OnSerialize(NetworkWriter writer)
    {
      base.OnSerialize(writer);
      writer.Write(_timerSync); //Write the timerSync value
    }

    public override void OnDeserialize(NetworkReader reader)
    {
      base.OnDeserialize(reader);
      _timerSync = reader.ReadInt32(); //Read the timerSync value on the client
    }

    //The quest system utilizes this method to know what Type we should use for the Objective Panel.
    protected override Type GetObjectiveType()
    {
      return typeof(ObjectiveTracker);
    }

    //This class handles the Objective Panel
    private class ObjectiveTracker : BaseQuestObjectiveTracker
    {
      private CompulsionQuestSurviveTenSeconds _lazy;
      protected override string GenerateString()
      {
        //This method "Lazily" returns the quest instance, basically if "_lazy" is null, it'll populate it with the current quest instance and then return it always.
        LazyQuest(ref _lazy);
        return Language.GetStringFormatted("QUEST_COMPULSION_SURVIVETENSECONDS", SECONDS_TARGET - (float)_lazy._timerSync); //Since _timerSync is networked, this will display properly on clients and server
      }
    }
  }
}
```
# Unity and MonoBehaviour

## Scene, GameObject, and Component
Every Unity game is running a scene that is composed of GameObject's which have a collection of components attached to them.
Components are the main source of game logic.
A MonoBehaviour is the main class you want to derive from to create your own GameObject component.

## MonoBehaviour Messages
Every instance of MonoBehaviour has some special functions that are called by the engine at specific events.
Here is the game loop: https://docs.unity3d.com/uploads/Main/monobehaviour_flowchart.svg

All MonoBehaviour callbacks are described in unity documentation: [Check under "Messages" header](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html)
But down below we'll talk about some of them.
## Constructing a MonoBehaviour

### Constructor
Generally, you shouldn't use constructors in classes derived from MonoBehaviour.
https://ilkinulas.github.io/development/unity/2016/05/30/monobehaviour-constructor.html


### Awake
Awake on your main plugin class occurs prior to all catalog init, so it should be used for any hooks or catalog events.

### Awake
Happens after all plugin mods are loaded in the appdomain and their metadata available  through the BepInEx Chainloader

## Update
The update is called every frame if the MonoBehaviour is enabled.

This is the most commonly used function to implement any kind of game script. Not every MonoBehaviour script needs Update. Usually, if you want to do something every frame, like checking user input, you should do this here.

## LateUpdate
Same as Update but gets called right after it.

## FixedUpdate
FixedUpdate has the frequency of the physics system; it is called every fixed frame-rate frame.
This means that depending on that Unity project it can be called multiple times in one frame.
You should only use this callback if you want to do anything physics-related.

## OnDestroy
Happens after component or its game object is destroyed.
You can destroy any component/game object with `Object.Destroy`

## Invoke
Calls a function after a specified amount of time.
It is for the best to avoid this method. If you want to invoke something with a delay use coroutine instead.

## InvokeRepeating

Calls a function after a specified amount of time and then continues to call that function with a given time interval between calls.
Avoid, same as Invoke.

## StartCoroutine

Starts an asynchronous process similar to a thread. Use this to repeat a function a specific number of times or Lerp a value without slowdown.

interesting article about coroutine vs threading: https://gamedev.stackexchange.com/questions/143454/unity-coroutine-vs-threads

## AddComponent

Adds a component to an object. This can be used to add extra functionality to a game object by making it run a script that it does not currently run.

## GetComponent

Get component returns a script / component that is attached to an object. This will throw an error if it is trying to access a component that an object does not have. Can be used to get other scripts on an object and access their variables and functions.
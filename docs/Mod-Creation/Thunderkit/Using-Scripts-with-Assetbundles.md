# Using Scripts with Assetbundles

## TODO FIXME : JUST REWRITE THAT WHOLE THING: USE THUNDERKIT AND EITHER USE THE ASMDEF WORKFLOW AND USE THE UNITY EDITOR COMPILER / OR SHOW HOW TO IMPORT YOUR C# PROJECT DLL ASSEMBLY IN YOUR UNITY EDITOR PROJECT DRAG AND DROP AND REFER THE SCRIPTS FROM THAT ASSEMBLY DIRECTLY

_(WIP)_

Tutorial on how to use custom scripts in AssetBundles.

## Before you start
* ### [First Mod](https://github.com/risk-of-thunder/R2Wiki/wiki/First-Mod)

Read up on how to create a BepInEx plugin as we will be utilizing one.

## Tools
* **Unity**
* **AssetBundle Browser** - Built in unity package

## Building the ExamplePlugin
Example classes of the plugin we will be using ==>

* **ExamplePlugin.cs** : _This is our plugin class._
```cs
using BepInEx;
using UnityEngine;
using RoR2;
namespace ExamplePlugin
{
    //BepInEx plugin information
    [BepInPlugin("com.examplemodder.exampleplugin", "ExamplePlugin", "1.0")]

    //make sure this inherits BaseUnityPlugin otherwise BepInEx will not recognize this.
    public class ExamplePlugin : BaseUnityPlugin
    {
        //our elevator prefab
        private GameObject _elevatorprefab;
        //the player's character master
        private CharacterMaster _playerMaster;

        public void Awake()
        {
            //get location of the plugin
            string pluginfolder = System.IO.Path.GetDirectoryName(GetType().Assembly.Location);

            //asset bundle to load
            string assetBundle = "myelevator";

            //prefab location inside asset bundle
            string prefab = "assets/prefabs/MyElevator.prefab";

            //load assetbundle then load the prefab
            _elevatorprefab = AssetBundle.LoadFromFile($"{pluginfolder}/{assetBundle}").LoadAsset<GameObject>(prefab);

            //start listening for onPlayerAdded
            PlayerCharacterMasterController.onPlayerAdded += OnPlayerAdded;
        }

        public void OnPlayerAdded(PlayerCharacterMasterController playerCharacterMasterController)
        {
            //no longer need to listen for any other player masters, we just need one player.
            PlayerCharacterMasterController.onPlayerAdded -= OnPlayerAdded;

            //Listen for the creation of the player body
            playerCharacterMasterController.master.onBodyStart += OnBodyStart;
        }

        public void OnBodyStart(CharacterBody body)
        {
            //check if master exists
            if (body.master)
            {
                //grab our master
                _playerMaster = body.master;

                //remove listener onBodyStart
                body.master.onBodyStart -= OnBodyStart;
            }
        }

        public void Update()
        {
            //listen for user input "1"
            if (Input.GetKeyDown(KeyCode.Alpha1))
            {
                //Check to see if the player body exists.
                if (_playerMaster)
                {
                    //get player position
                    Vector3 playerPosition = _playerMaster.GetBodyObject().transform.localPosition;

                    //create new instance of prefab "MyElevator(Clone)"
                    GameObject newElevator = GameObject.Instantiate(_elevatorprefab);

                    //example of accessing child component
                    ElevatorHandler elevatorComponent = newElevator.GetComponentInChildren<ElevatorHandler>();

                    //set new instance of prefab to playerPosition
                    newElevator.transform.localPosition = playerPosition;
                }
            }
        }

    }
}
```

* **ElevatorHandler.cs** : _This is our elevator component which is in charge of moving it around._

```cs
using System;
using UnityEngine;
namespace ExamplePlugin
{
    [Serializable]
    public class ElevatorHandler : MonoBehaviour
    {
        [Header("ElevatorHandler Example Component")]
        [Space]
        [Header("!!!Position of this GameObject will reset to zero, keep this GameObject Nested!!!")]
        [Space]

        [Tooltip("ElevatorConfig Prefab")]
        public ElevatorConfig elevatorConfig;


        //what direction are we moving in?
        [UnityEngine.HideInInspector]
        public bool isMovingFoward;

        //additive position modification
        private Vector3 _velocity;

        //max distance can travel
        private float _max_distance;

        //used as reference when calculating distance.
        private static Vector3 _zero = new Vector3(0, 0, 0);
        public void Awake()
        {
            //check if Config Exist
            if (elevatorConfig)
            {
                _velocity = elevatorConfig.elevatorVelocity;
                _max_distance = elevatorConfig.maxDistance / 2;
            }
            //otherwise set default config
            else
            {
                _velocity = new Vector3(0, 0.3f, 0);
                _max_distance = 25;
            }
            //set default position
            this.transform.localPosition = _velocity * -_max_distance;

            //set moving direction
            isMovingFoward = true;

            Debug.Log("New Elevator Created!");
        }

        public void Update()
        {
            //check what direction it is traveling
            //then apply the velocity to the position
            if (isMovingFoward)
                this.transform.localPosition += _velocity;
            else
                this.transform.localPosition -= _velocity;

            //check distance traveled
            if (Vector3.Distance(_zero, this.transform.localPosition) > _max_distance)
                //if traveled too : far flip directions!
                isMovingFoward = !isMovingFoward;
        }
    }
}
```
* **ElevatorConfig.cs** : _This is the elevator config template file_
```cs
using UnityEngine;
namespace ExamplePlugin
{
    public class ElevatorConfig : ScriptableObject
    {
        [Header("ElevatorConfig Template File")]

        [Tooltip("The velocity of which this travels")]
        public Vector3 elevatorVelocity;

        [Tooltip("The max distance allowed to travel")]
        public float maxDistance;
    }
}
```
### !!! Build these files into a BepInEx Plugin !!!

<a name="method1"></a>
# Steps
* Start up a project in unity to be the base of your asset bundle.

* Create a Folder called Scripts

* Create a Assembly Definition

* In the Assembly Definition that you just created change the 'Name' to your plugin Assembly, In this example it is "TutorialExample" 

* Drag the scripts you want to use in the AssetBundle over to this Folder

* Click on one of your scripts and you will see that it will be referring to your assembly file.

* Create a prefab , in this case I created a parent GameObject with a child called Platform.

* In the prefab I placed my Monobehaviour as a component in the Platform.

* Open up AssetBundle Browser and Drag your prefab into a new bundle

* Build your bundle and copy it over to your plugin folder.

* Run your plugin and see if it works...

* Congrats! you learned how to create asset bundles with scripts, go make something crazy.
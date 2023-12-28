# Prefabs
From the Unity Documentation :
> Unity’s Prefab system allows you to create, configure, and store a GameObject complete with all its components, property values, and child GameObjects as a reusable Asset. \
The Prefab Asset acts as a template from which you can create new Prefab instances in the Scene.

RoR2 isn't an exception and uses Prefabs to load most of its game objects, from bodies of all entities (classes, monsters...) to projectiles, skills and so on.

## Getting the values of the Prefabs
Dumper code here : https://gist.github.com/xiaoxiao921/9d7f5aa50676b475f3cf750b5ef57955 \
Dumper code version with state configuration + expanded collection values : https://gist.github.com/yekoc/d632468df9ebc53641170301ac2f67ef

Already made dump here : 
[prefabs.7z](https://github.com/risk-of-thunder/R2Wiki/files/8508041/prefabs.zip)
ITS ACTUALLY A 7z FILE !!! change extension from .zip to .7z and use 7zip or winrar for opening

## A more natural approach of reading the values of prefabs is by using Thunderkit with its Addressable Browser

TODO FIXME: LINK
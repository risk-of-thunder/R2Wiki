# Server side and client side mods

When uploading mods to the Thunderstore, you can tag them both as client-side and/or server-side. This means that the mod needs to be installed on the multiplayer host's computer (server-side) and/or the other players' computers (client-side) in order to work properly.

These tags themselves don't do much, they only help with searching/filtering in the Thundertore. On the code level, you can declare your mod's attitude towards multiplayer runs (this is optional). You can do this by decorating your main class (the one derived from `BaseUnityPlugin`) with the [R2API NetworkCompatibility attribute.](https://github.com/risk-of-thunder/R2API/blob/master/R2API/Utils/NetworkCompatibility.cs)
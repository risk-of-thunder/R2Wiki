# Merging Materials

This guide you help you fellow developer get your model nicely textured in-game with no issues. 

# Requirements
image editing software (this guide uses Photoshop) and Blender - preferably 2.8+.

## Getting Started
I'm gonna assume you already have your model extracted from the game and imported in Blender. (Don't ask anyone for a guide for this, because it's a different method for each game).

So for this guide I'll be using Shiva from FF VII Remake.

Here I have her mesh with several materials.

![image](https://github.com/user-attachments/assets/f89dd8db-b987-427a-ac37-af04349eb313)

First we'll separate each part of her mesh that has a different material attached to it.
Press Tab to go into Edit mode, and select all by pressing A once.

![image](https://github.com/user-attachments/assets/ea467ec6-cd98-4dcb-be90-83daa3c84826)

Now Press P-> Separate By Material

![image](https://github.com/user-attachments/assets/b81a4d6e-40f3-4fee-b73a-457b375eb465)

Tip: After unrapping you can use an addon to orgnize your UVMap such as uvpackmaster2, it not only looks nice, but also correctly resizes, distances the uv islands and makes sure they don't overlap. It is paid, either download it somewhere or find an alternative.

It looks like this when using uvpackmaster2:

![image](https://github.com/user-attachments/assets/e2b3e16f-d12d-47df-afe5-e9c85b6f4bb5)

Now we have all meshes with their materials ready to be merged, and there's two ways of doing this.

- The first method is baking a new texture, which is easier, but loses a bit of texture quality. 

- Second method is merging all textures into one, maintains quality, but it's a bit time consuming.

NOTE: Second method is always recommended, baking a new texture is only recommended when you have a model with too many materials which makes second method process tedious.

## Baking a new texture

We'll go over each material and add a new texture of size 2048 no Alpha. If you want more quality you can make it 4096+, but keep in mind that this will increase the texture file size, we can reduce it's size in unity later though.

![image](https://github.com/user-attachments/assets/0b8fb3c9-ac3e-46aa-81c6-d702b2a5dcda)

Then we'll go over each mesh and add a new UVMap.

![image](https://github.com/user-attachments/assets/e46194ad-a2bd-4b9d-8dc3-d29f5ee548fa)

Now make sure each material has the new texture selected and each mesh has it's new UVMap selected. After that, select all meshes and go into Edit mode.
Select all by pressing A and open a new UV Editor tab so we can view all the meshes UVs. 

![image](https://github.com/user-attachments/assets/aa386634-ce06-4091-8317-8f712bd6d9a1)

In the UV Editor tab we'll select all UVs by pressing A once and then, in the 3d View tab in Edit mode, we'll unrap the UVs by pressing U and selecting either Unrap or Smart UV Unrap, the latter being better, but sometimes just Unrap helps. If you do use Smart UV Unrap make sure you set IslandMargin to 0.003+. Play around with the settings until you get a good UV unrap. If that doesn't help lookup some guides on uv mapping.

![image](https://github.com/user-attachments/assets/60d3c4c3-2490-4f88-b80b-d0956f689cf5)

Now that we have our new UV Map, all we have to do is bake a new texture into this new UV Map. Go to Render Properties tab, select Render Engine Cycles. Adjust the settings the same as the screenshot. NOTE: Adjust margin to be half your Island Margin you set when you unrapped your UV.

![image](https://github.com/user-attachments/assets/55ae3027-cf04-4c89-9495-a440b087555c)

Now click Bake and watch the magic happen. After blender is done baking your new texture, you'll notice no difference. That's because you havent attached the new texture node as the Base Color of your shader and your new UVMap isn't active. So go ahead and do that for every mesh. You should delete the other UVMaps so you don't have issues when you import your mesh in Unity. 

Troubleshoot: In case it doesn't work and some mats aren't being baked into the new texture. 

1. Sometimes Blender doesn't like you, and it may ignore that the new texture is actually selected. So make sure you click out to deselect it and select it again.

2. Retrace your steps. Make sure everything is correct, and always double check if any mesh has multiple materials.

3. Make sure all meshes have the new UV Map selected but not active. 

![image](https://github.com/user-attachments/assets/9638136c-7a9c-4322-b937-58ed958b650d)

Now all you have to do is select all meshes and press Ctrl+J to merge them together.
Done.
NOTE: Don't forget to save your baked texture, because unless you have selected to save External Data you will lose it!

![image](https://github.com/user-attachments/assets/9d5c85f5-7311-4145-aa4f-5cae4408736a)

## Merging textures

This one is simple and easy, just time consuming. 

First thing we're gonna do is get all the textures loaded in Photoshop (or whatever other image editing software you have).

We're gonna set them side by side or on top of eachother (preferably side by side to make it easier later).

![image](https://github.com/user-attachments/assets/d9d303b2-93b7-4d69-b8b5-e1c9f1baa789)

Write down the canvas size, we're gonna need it later.

Now in blender, make sure you have the separated meshes each with their own material.

Select a mesh, and add the new texture (with all merged textures) to the texture node and link it to your shader.

![image](https://github.com/user-attachments/assets/7ff58531-34b1-4c7c-a575-1c669d61cc42)

Go into Edit Mode and select all, and have a UV Editing window open. There we will rescale the UV to fit the new texture, also you'll notice the UV will get stretched.

Shiva's face texture (by itself) has the size of 2048x2048, and since I merged the textures side by side I only have to rescale the X axis of the UV.

I'll get the width of the original texture (2048) and divide by the width of the new merged texture which in my case is 5120. The result is 0.4, and that is what I will rescale the X axis of my UV by.

In the UV Editing tab have your entire UV selected and press S+X and input the value you got, then drag the UV by pressing G+X until it matches the texture.

![image](https://github.com/user-attachments/assets/e9872e23-fa30-4555-a1e5-3ceabb9e8082)

![image](https://github.com/user-attachments/assets/96b2a139-88b0-4bff-960e-90faa9b2e19f)

Do that for every mesh, then merge them by pressing Ctrl+J and you're done.

After you merged you texture, it will probably be above 2k if your model is a real well done HD model.

So we can compress it a bit in unity, after importing. I recommend only reducing it's max size, no need for compression, unless your texture is like 15-30 MB.

![image](https://github.com/user-attachments/assets/bd93d9c5-291d-4e69-9fc3-e8e6c7caa529)

That's it, I hope this guide helped you get your mods going and you learned something new.

Any questions can be directed to breadguy#5000, knowledge about everything is known and no question will be unanswered. Thank you bb.
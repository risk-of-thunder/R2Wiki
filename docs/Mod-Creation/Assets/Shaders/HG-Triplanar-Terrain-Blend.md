# HG Triplanar Terrain Blend
***A detailed tutorial on understanding Triplanar Terrain Blend, one of HG's shaders that is most commonly used for terrain.***

Triplanar Terrain Blend (TTB) is a shader that projects textures and other inputs onto a mesh without caring about the UV coordinates of said mesh. It is very good at generating natural looking terrain textures such as the grass on Titanic Plains or the twisting tree roots on Sundered Grove. This shader requires a lot of trial, error, and experimentation to get good results due to its seemingly random nature. It is recommended to study the already existing TTB materials in RoR2 via Addressables or Runtime Inspector to get a better feel of the type of material you are going for.

---

## RGB Channels
TTB has three main inputs: the RGB channels that contain their respective textures and values. There are other inputs that aren't present in these channels but it'll be covered later in the guide.

![](https://cdn.discordapp.com/attachments/492876113454956554/1014327535896641616/unknown.png)

The Red channel has side and top textures while the other channels only have one texture. Which Red Channel texture gets used depends on the mesh normals. The shader takes the values and textures in each channel and procedurally generates a unified texture for the mesh.

There are other values in each channel so these will be covered individually.

---

### Smoothness
Higher values of Smoothness gives the material a reflective backlight. The backlight is affected by surrounding colors and materials so it is good for objects such as ice.

**Terrain with no Smoothness VS Terrain with max Smoothness.**

![](https://cdn.discordapp.com/attachments/492876113454956554/1014346787915571200/unknown.png)

---

### Speculars
The Specular sliders controls the reflectiveness of a particular channel. But before looking at examples, you need to understand key art concepts. 

What controls an object's "reflectiveness" is the amount of light bounced off of it that reaches our eyes... or well the camera. This contributes to the reflectiveness of a smooth metal ball or ~~your forehead~~ polished marble. A specular is the most direct point where the bounced light is hitting your eyes. It is that little bright white dot you notice on reflective materials

![](https://cdn.discordapp.com/attachments/492876113454956554/1014341904739090514/unknown.png)

The Specular Strength controls how reflective that channel's texture is while the Specular Exponent controls how big the specular of the reflection is. Here is an example with Sundered Grove's sap/fungus. Goes to show that no matter how weird the material, it follows the same rules.

![](https://cdn.discordapp.com/attachments/492876113454956554/1014345930318819429/unknown.png)

---

### Bias

Bias controls how "dominating" each channel is. All the channels share the same mesh so a value needs to be given to each channel to tell them how much they can step on each other. The higher the bias, the more space that channel will take up. However, a difference in bias can be so big that a channel's texture may never be present, be careful!

---

### Vertex Colors

Each channel in the TTB material can be controlled if you have "Use Vertex Colors Instead" checked. This is so that each channel refers to the vertex colors on your mesh as a reference of where each of the textures should (approximately) go. This goes without saying that the Red channel would use the Red vertex colors and so on. You can paint a mesh's vertex colors in a 3D modeling program such as Blender.

![](https://cdn.discordapp.com/attachments/492876113454956554/1014354792233050142/unknown.png)

---

### Bias Green to Vertical

This just makes it so that the Green channel is mainly prevalent on the vertical parts of the mesh (the planes of the mesh are generally facing upward). This is also why the Green channel is usually reserved for grass or anything else you would expect being on top of the mesh, even if Bias Green to Vertical is off.

---

## Everything Else
Here is everything else present in the shader that doesn't necessarily involve the RGB channels.

---

### Normal Texture and Strength

For those who don't understand basic shader or modelling terminology, Normals are a "depiction of the orientation of a polygon's surface". This is so the mesh can have more detail whilst also not wasting computing power on extra vertices. You could say its a lighting illusion of some sort. The Normal texture controls how the Normals would look on the mesh while the Normal strength controls how prevalent they are. (Please Google this if you are that curious.)

---

### Splatmap

Think of the Splatmap as extra "paint" you want to splatter all over the surface of your mesh. You input a texture to splatter all over the mesh surface to give it more flare.

---

I (Jace) don't completely understand everything with this shader yet, but I will keep this page updated as I go.
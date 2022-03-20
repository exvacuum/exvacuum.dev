---
layout: post
title: "Sketch-Like Jitter in Unity Using the URP Shader Graph"
categories: [Game Development]
tags: [Shaders, Unity, Game Dev, Design]

author: silas-bartha
---

When I was participating in the Conestoga College Virtual and Augmented Reality Lab (VARLab)'s 2022 Unity workshop, I was tasked with creating a game in 3 days using the Unity engine. I'll be posting more about my experience with that later, but for this post I just wanted to share the details behind one of the more technical details I implemented into my final solution: A sketch-like jitter effect on the plane-based creatures in the game. The result looks a little something like this:

![Wolf in Moose Tracks with Jitter Shader Applied](/assets/images/gamedev/unity-sketch-shader/final-result.gif){: .bordered}

> Note: You can find my game *Moose Tracks*, along with its source code on my site's [software page](/software#moose-tracks).

This shader uses pseudorandom noise to slightly offset the vertices of the mesh it is applied to in the *x* and *y* axes. It also allows for the use of a normal map for adding a "wrinkled paper" effect to the material. Without further ado, let's get into the details of the shader. For the purposes of this post I will assume you are familiar with how to create a shader in Unity's Universal Render Pipeline and how to add properties and nodes via the shader graph.

Create a URP lit shader graph and give it a name you feel is appropriate. I chose "Sketch" but it's really up to you. Once you have opened the graph, add two float properties and call them "Jitter Speed" and "Jitter Smoothness", and set their default values to 3 and 3 respectively. The speed property will control how many "jitters per second" the shader will produce, and the smoothness property will control how strongly time will affect the jitter effect. Setting both of these values high results in an effect similar to rippling water.

Now we can begin adding nodes to our graph. First we will add a **Time** node to control the generation of the noise used for the jitter. Then we connect the **Time** output of the **Time** node to a new **Multiply** node. Since the value of **Time** is provided as a float, we multiply it by Jitter Speed before rounding the result down with a **Floor** node. Dividing the result by the Jitter Smoothness property gives us the appropriate time-based value for the noise generation.

![](/assets/images/gamedev/unity-sketch-shader/2022-03-08-07-34-37.png){: .bordered}

From here we have to take an extra step and feed the output of the **Divide** node into a new **Tiling and Offset** node, and offset it by the *object-space* position of the vertex as supplied by a **Position** node. Now the jitter will also account for the existing position of a vertex when creating the noise.

![](/assets/images/gamedev/unity-sketch-shader/2022-03-08-07-54-47.png){: .bordered}

Now, add a new float property to your shader. Call it "Jitter Density", and give it a default value of 6. This will control the scale of the noise used for the jitter effect. Next create a **Gradient Noise** node and connect the output of the **Tiling and Offset** node to the **UV** input. Connect the new Jitter Density prperty to the **Scale** input.

![](/assets/images/gamedev/unity-sketch-shader/raw-noise.gif){: .bordered}

If your noise preview does not look like this, make sure you've configured your properties correctly. This noise will range between 0 and 1, however we want the offset to be beetween -1 and 1, so let's connect a new **Subtract** node and subtract 0.5 to make it range between -0.5 and 0.5, and then use a **Multiply** node to double that value. Finally, we want to be able to control how strongly our jitter manifests in 3D space, so let's add a new float property called "Jitter Strength" and give it a default value of 0.005. This value will likely need to remain pretty small.

![](/assets/images/gamedev/unity-sketch-shader/scaled-noise.gif){: .bordered}

Now we are almost done with the vertex displacement. We just need to actually apply it. Feed the output of the **Position** node you created earlier into a new **Split** node, and pass each of the *R*(x) and *G*(y) outputs into new **Add** nodes, along with the output of the final **Multiply** node. Finally, create a new **Combine** node, and put the outputs of the **Add** nodes back to their respective components, and pass through the unmodified components from the **Split** node as well. Then put the output of this node into the **Position** property of the **Vertex** block.

![](/assets/images/gamedev/unity-sketch-shader/vertex-displacement.gif){: .bordered}

And that's the hard part out of the way. Next, add 2 Texture2D properties called "Base Texture" and "Normal Map". In the graph inspector, enable the "Alpha Clip" and "Two Sided" options. Connect the Base Texture property to a new **Sample Texture 2D** node, and feed the *RGBA* and *A* properties to the shader's fragment block.

![](/assets/images/gamedev/unity-sketch-shader/2022-03-08-08-47-26.png){: .bordered}

Finally, to achieve a wrinkled paper effect, connect the Normal Map property to another **Sample Texture 2D** node. Make sure this one has *Type* set to "Normal". Connect the *RGBA* output of this node to a new **Normal Blend** node along with the world-space normal vector from a new **Normal Vector** node. Finally, feed the output of the **Normal Blend** node into a new **Normal Strength** node, along with a new float property called "Crumpledness". This will control how strong the normals are on this material. I set the default to 0.3, but choose something you find appealing. Connect the output of this node to the *Normal* input of the shader's fragment block.

![](/assets/images/gamedev/unity-sketch-shader/2022-03-08-08-58-57.png){: .bordered}

And with that, our sketch shader is complete!

![](/assets/images/gamedev/unity-sketch-shader/shader-complete.gif){: .bordered}

For best results, I use this shader on meshes like this subdivided plane, where there are enough vertices packed together to look convincing.

![](/assets/images/gamedev/unity-sketch-shader/2022-03-08-09-03-30.png){: .bordered}![](/assets/images/gamedev/unity-sketch-shader/wolf-mesh.gif){: .bordered}

I hope you found this explanation of this shader useful! I learned a lot about the Unity Shader Graph while creating it.

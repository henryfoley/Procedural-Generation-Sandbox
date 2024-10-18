![ProcMndk_Unreal_Beauty_1](https://github.com/user-attachments/assets/3f7763df-048f-45a1-9036-71fc7751b7ab)

# Description
## Stated Goal
My goal is to create a procedural framework that efficiently populates landscapes with assets based on terrain characteristics, allowing for easy customization and fast iteration. This framework will seamlessly integrate features from both Houdini and Unreal, while maintaining a high level of realism.

## Background
Through a conversation with a mentor over the summer I realized that I might have been spreading myself a bit too thin in terms of technical art disciplines. Through our conversation I decided it would be best to focus in on procedural generation and shaders, and the linking of the two.

After that decision I went on a hike up Mount Monadnock in New Hampshire and got the idea to recreate it in Unreal using the new PCG system and Houdini. Over the past month and a half I’ve been watching a ton of tutorials, browsing forums and experimenting a lot with PCG.

Now I’ve finally got a project in a state to show-off. It’s not finished and there’s still a LOT that I’d like to get done, but I figure having others see it will be a good motivation to finish.

# Unreal
## PCG System

![PCG_Graph-1024x534](https://github.com/user-attachments/assets/6aee714e-00d2-4fcd-aa72-7935dda5d4ca)
Main Graph

Using a Landscape and IDMAP input users are able to generate layers that can be used to spawn assets on the landscape. Within each of these layers is a Sub-Graph with a set of customization options that allow the user to control what is being placed and how. Currently eight layers are supported, but this can easily be expanded upon.

![ProcMndk_PCG_LayerGraph-1024x122](https://github.com/user-attachments/assets/7188f374-6bef-47c5-8585-bc6d9d306edd)
Layer Sub-Graph

## Layer Data Assets
Each layer’s Sub-Graph is controlled through a Layer Data Asset. This structure allows for quick and easy editing of the layer’s content. Meshes can be added to a layers ‘mesh array’, which places each of them according to a enable toggle and a customizable Weight value. Points in the layer can be pruned based on their bounds.

![ProcMndk_LayerData-1-1024x384](https://github.com/user-attachments/assets/116a7aa1-0f29-44d3-ab75-927c1f47990d)

Mesh transforms are randomized within a Min/Max range. A custom PCG Blueprint gives them the option to be placed oriented along the landscape normal or oriented facing upwards.

## Landscape
The Landscape Material is based on the same IDMAP that the PCG system works with, allowing you to customize the landscape color for each layer. This system was heavily inspired by the one created for Project Pegasus by George Hulm.

It uses three sets of texture arrays to store the texture maps for each layer and bilinear blending to create a natural smooth transition between those layers. Using Curve Atlases, the albedo of each layer can be tinted. Macro noise also breaks up repetition.

# Houdini
## Path Generation

![ProcMndk_Unreal_Pathfinding-1024x474](https://github.com/user-attachments/assets/a8bbbe08-2d5c-475a-b2f7-19f7bf5019a9)
POI Spline above with HDA Generated Spline below

For this project I decided to revisit a smaller project I did several months prior, in which I created an HDA for finding the shortest distance between two points on a landscape. I added a slew of additional costs for the pathfinding node to consider and the ability to support paths with multiple points of interest.

![ProcMndk_Houdini_Pathfinding-1024x441](https://github.com/user-attachments/assets/ce35cf13-db8a-40aa-8a64-56e83646b3ee)
Green Areas attract path, Blue areas repel

One additional feature that is still a WIP are custom masks used to define pathing. I haven’t found a good way to communicate this data between UE/Houdini, so for now it is only available in the HDA.

# Future Development
## Optimizations

![ProcMndk_Unreal_Imposters-1024x501](https://github.com/user-attachments/assets/321e7745-3dbd-4aed-99dd-ec70a021a1bf)
70 FPS w/Imposters

![ProcMndk_Unreal_Nanite-1024x479](https://github.com/user-attachments/assets/d3f2fad3-1655-4d2f-9ed6-3ac7806138db)
16 FPS w/Nanite

The scene is SO SLOOOWWW at the moment, in large part due to the assets that I’m choosing to spawn. Nanite, while powerful, does have its limits and I seem to have reached them. I’m currently working on transitioning the trees in the scene to a more traditional LOD approach using Imposters for far distance instances. That has led to a decent improvement in frames, but there is still much work to be done on this.

While the PCG system itself is not that slow, as I get into higher point counts optimizations are going to be more of a concern.

## Expanded Layer Generation System

At the moment I’m currently using the Landscape Mask Generation HDA created by George Hulm at SideFX for Project Pegasus. While this system works fairly well, I do think there are some improvements I might be able to make upon it. Namely changing the way how layers are ordered, which at the moment is done by rearranging nodes in the graph.

![ProcMndk_Houdini_LayerMasks-1024x438](https://github.com/user-attachments/assets/d285b029-4cb1-46b7-9b02-7a3a8032e98f)

I see potential in moving all the ordering and mask type choices to a single HDA that allows users to make all those changes in a custom GUI. This may involve using some Python, which would be a good opportunity to learn more about the language’s application in Houdini.

## Spline Based Path Generation
At this moment all the HDA generated spline does is carve out a path within the PCG System, I think this can be expanded on a lot. Some ideas include landscape deformation, creating detailed paths, and rivers.

## Data Asset & Blueprint Expansion
What else can be included in a Data Asset to influence layer generation? Can Data Assets be nested within other to allow for easier, more controllable vegetation layers? I.e a Layer Data Asset containing Data Assets for Trees, Rocks, Grass, etc…

Can the Blueprint and PCG system be expanded upon to allow for a less ‘hard coded’ approach to layer count? Theoretically there could be up to 255 layers in each IDMAP.

And lastly, can the information used to feed the material layer system also be fed to the PCG system? For this I might have to explore Editor Utility Widgets. I’d love to have a single UI control all these systems seamlessly.

# Challenges
## Unreal Landscape Layers
Originally I wanted to use the Unreal Landscape Layer system to feed the PCG system rather than texture data. But the Texture Array approach, while offering greater ease of adding layers, does not seem to work with the Landscape Layer system. From what I’ve gathered these layers have to be hard coded into the material.

But hey I could be wrong! Researching this has been very tough as there is not a whole lot out on this style of landscape material.

## PCG Limitations/Bugs
The PCG system, while amazing, is still in its infancy. I’ve encountered a couple of bugs that have hindered my progress (texture data node I’m looking at you). The system is also not as seamless as say regular Blueprint and the Material Editor, but I’m sure the very smart people at Epic Games will get it there soon enough.

Finding learning resources on PCG has also been quite difficult. But I will mention that the Unreal Source Discord has a PCG chat that has been instrumental in solving a lot of my problems.

# References
These are the people and tutorials that have made this project possible so far:

– George Hulm & the team at SideFX who produced an amazing video series on Project Pegasus

– Roman K in the Unreal Source Discord. He’s made some really great tutorials and answered a ton of my questions in the PCG channel

– ExitSimulation on Youtube. He’s done some very helpful tutorials

– All assets are either Quixel Megascans or Project Nature

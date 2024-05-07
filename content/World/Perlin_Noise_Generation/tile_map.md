# Tile Maps for 2D Worlds Generation

```
Godot Version: 4.2.1
Tested on: Janurary 25, 2024
Created by Avery Fernandez
```

This recipe was created and tested on Godot 4.2. It covers how to create a 2D world using Perlin noise and tile maps.

## Create the World Scene

First, let's go ahead and begin to create the world scene. Let's create a new `2D Scene`. Name the root node `World` and save the scene as `world.tscn`. 

## Create the Tile Map

Next, let's create a `TileMap` node (it should be a child of `World` node). We will use this node to create the world.

### Adding the Tile Set

Go ahead and click on the `TileMap` node. In the `Inspector`, click on the `Tile Set` property. This will open the `Tile Set` editor. Click on the `New Tile Set` button. This will create a new tile set.

Currently, the `TileMap` node will be using a tile size of `16 px by 16 px`. You will want to change that to the resolution of your tiles. For this recipe, we will use `64 px by 64 px`. You can change this by clicking on the `TileMap` node and click on `TileSet` in the `Inspector`. Then, change the `Tile Size` property to `64 px by 64 px`.

Now, while the `TileMap` node is still selected, at the bottom of the screen select the `TileSet` tab. This will open the `TileSet` editor. You can either add tiles by dragging and dropping images into the editor, or by clicking on the `Import` button and selecting the images you want to use. For this recipe, we will use the images [textures.zip](https://github.com/AveryUALibrary/Godot-Cookbook/blob/f858305335a2277218363dc860feddd978a3e13c/content/World/perlin_noise_tiles.zip)

> **Note:** When the message "The atlas's texture was modified. Would you like to automatically create tiles in the atlas." appears, go ahead and click yes.

We recommend going through the individual tiles and setting the `ID` property to a number that makes sense to you. For example, you may want to set the `ID` of the grass tile to `1` and the `ID` of the water tile to `0`.

The values of the `ID` for this recipe are as follows:
```
0 - Water
1 - Sand
2 - Grass
3 - Rock
```

Now, you have a `TileMap` with a `TileSet` that contains the tiles you want to use.

### Adjusting the Tile Map Size

For those who realize that the tile map is either too massive or too small, I would recommend not changing the `Tile Size`. Instead click on the `TileMap` node and change the `Scale` property to the desired size. This will scale the tile map to the desired size.

## Create the World Generation Script

Now, let's create the script that will generate the world. Right click on the `TileMap` node and select `Attach Script`. Name the script `world_generation.gd`.

The way we will generate the world is by using Perlin noise. Perlin noise is a type of noise that is used to create smooth random values. It is often used to create terrain in games. We will use a combination of Perlin noise and tile maps to create our world.
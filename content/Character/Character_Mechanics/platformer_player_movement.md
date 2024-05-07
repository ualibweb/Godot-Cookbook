# Platformer Player Movement
```
Godot Version: 4.2.1
Tested on: February 8, 2024
Created by Sebastian Shirk
```
* This recipe will show you how to create a simple platformer character that can move left and right, jump, and fall. We will also include some custom mechanics such as wall-jumping/sliding, double jumping, and dashing. This recipe will also show you how to create a simple platformer level to test your character in.

## Creating the Character Basics 
* Create a new scene and make it a `CharacterBody2D`
* Add a `Sprite` as a child of the `CharacterBody2D` and set the `Texture` to a character sprite.
* Add a `CollisionShape2D` or a `CollisionPloygon2D` as a child of the `CharacterBody2D` and set the `Shape`. If you are using a `CollisionPloygon2D` you will need to set the `Polygon` points to match the shape of the character sprite.
* Finally, add a `Camera2D` as a child of your character
* With our character created, we can now add a script to the `CharacterBody2D` to control its movement.
* Click on your `CharacterBody2D` and click the `Attach Script` button or you can right-click on your `CharacterBody2D` and select `Attach Script`.
* Before clicking `Create`, change the `Template` to `CharacterBody2D: Basic Movement` and click `Create`.
* This will give us a basic script to control our character's movement.
* I recommend using this script as a starting point and adding your custom mechanics to it as its a good foundation for a platformer character.

## Creating the Level
* Create a new scene and make it a `Node2D`
* Add a `TileMap` as a child of the `Node2D` and set the `Cell Size` to the size of your tiles.
* Add a `TileSet` to the `TileMap` and add some tiles to it.
* With the `TileMap` selected, click the `Tile` button and select a tile from your `TileSet` and start painting your level.
* Add a `Physics Layer` to the `TileMap`
* In your `TileSet Editor`, you can set the `Collision` of any tile you want by selecting the tile, selecting `Physics`, select whatever physics layer you are using and create your collision box that you wish to use. <br>
![alt text](<2024-02-09 14-59-17.gif>)
* In this gif we are setting the collision for the ground tiles.
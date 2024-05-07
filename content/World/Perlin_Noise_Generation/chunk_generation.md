# Chunk Generation with Player Tracking

```
Godot Version: 4.2.1
Tested on: Janurary 29, 2024
Created by Avery Fernandez
```

While we have the world generation working, we still have a problem. The world is infinite, but we are only generating a small portion of it. We need to generate more chunks as the player moves around. We will do this by tracking the player's position, and generating chunks around the player.

## Track the Player's Position

We only want to have chunks be generated around the player's location. We will do this by tracking the player's position through something called a global script. A global script is a script that is always running, and can be accessed from any other script. We will use this to track the player's position.

### Create the Global Script

First, we need to create the global script. Create a new script called `globals.gd` in the `res://` directory by right clicking on the `res://` directory, clicking `New Script`, and naming it `Globals.gd`. Open the script, and add the following code:

```gdscript
signal player_moved(player_position: Vector2)

const CHUNK_SIZE = Vector2(16, 16)
const VIEW_DISTANCE = 4
```

This script is setup to store our global variables like the chunk size and view distance. We also have a signal called `player_moved` that will be emitted when the player moves. We will use this signal to generate chunks around the player.

### Connect the Player's Movement to the Global Script

Now, we need to connect the player's movement to the global script. You can either do this by adding the following code to your player script in the `func _physics_process(delta: float):` function:

```gdscript
Globals.player_moved.emit(global_position)
```

Or, if you don't have a player, we can create one really quickly by doing the following:

#### Create the Player

At the top of the screen, click the `+` in the middle and then click `2D Scene` to create a new scene. 

Now, we should have a new scene with a `Node2D` in it. Right click on the `Node2D` and click `Change Type`. In the search bar, type `CharacterBody2D` and click on it. Go ahead and rename the node to `Player`.

Next, let's add a sprite to the player. Right click on the `Player` node and click `New Child Node`. In the search bar, type `Sprite2D` and click on it. Now, click on the `Texture` property in the `Inspector` and click `Load`. Navigate to the `res://` directory and select the `icon.svg` file.

Let's add a collision shape to the player. Right click on the `Player` node and click `New Child Node`. In the search bar, type `CollisionShape2D` and click on it. Now, click on the `Shape` property in the `Inspector` and click `New RectangleShape2D`. Now, click on the `Size` property in the `Inspector` and change the `x` value to `128` and the `y` value to `128`.

We now need a camera to follow the player. Right click on the `Player` node and click `New Child Node`. In the search bar, type `Camera2D` and click on it.

Finally, we need to add a script to the player. Right click on the `Player` node and click `New Script`. Name the script `Player.gd`. Open the script and add the following code:

```gdscript
extends CharacterBody2D

const SPEED = 300.0

func _physics_process(_delta):
	# As good practice, you should replace UI actions with custom gameplay actions.
	var direction = Input.get_vector("Left", "Right", "Up", "Down").normalized()
	if direction:
		velocity = direction * SPEED
	else:
		velocity = Vector2.ZERO

	move_and_slide()
	Globals.player_moved.emit(global_position)
```


## Generate Chunks Around the Player

Now that we have a way to track the player's position, we can generate chunks around the player. We will do this by creating a dictionary that stores the chunks that are loaded. We will then generate chunks around the player, and unload chunks that are too far away from the player.

### Create the Dictionary

First, we need to create the dictionary. Add the following code to the `world_generation.gd` script:

```gdscript
var loaded_chunks = {}
```

This dictionary will store the chunks that are loaded. The key will be the chunk location, and the value will be the chunk.

This allows us to only generate chunks that are needed, and unload chunks that are not needed.

### Loading Chunks

Now, we want to write code that generates a chunk. The chunk will be generated at the chunk location, and will be added to the `loaded_chunks` dictionary. The chunk is a square of tiles, so we will need to loop through the tiles in the chunk and set the tile to the correct tile.

Add the following code to the `world_generation.gd` script:

```gdscript
func load_chunk(chunk_position):
	if chunk_position in loaded_chunks:
		return
	var chunk = {}
	loaded_chunks[chunk_position] = chunk
	for x in range(chunk_position.x * Globals.CHUNK_SIZE.x, (chunk_position.x + 1) * Globals.CHUNK_SIZE.x):
		for y in range(chunk_position.y * Globals.CHUNK_SIZE.y, (chunk_position.y + 1) * Globals.CHUNK_SIZE.y):
			select_tile(Vector2(x, y))
```

This function takes the chunk position and checks if the chunk is already loaded. If it is, it returns. If it is not, it creates a new chunk and adds it to the `loaded_chunks` dictionary. It then loops through the tiles in the chunk and sets the tile to the correct tile.

### Unloading Chunks

Now, we want to write code that unloads a chunk. The chunk will be removed from the `loaded_chunks` dictionary. This will allow the chunk to be garbage collected, and free up memory.

Add the following code to the `world_generation.gd` script:

```gdscript
func unload_chunk(chunk_position):
	if chunk_position not in loaded_chunks:
		return
	for x in range(chunk_position.x * Globals.CHUNK_SIZE.x, (chunk_position.x + 1) * Globals.CHUNK_SIZE.x):
		for y in range(chunk_position.y * Globals.CHUNK_SIZE.y, (chunk_position.y + 1) * Globals.CHUNK_SIZE.y):
			set_cell(0, Vector2i(x, y), -1, Vector2i(0, 0), 0)
	loaded_chunks.erase(chunk_position)
```

This function takes the chunk position and checks if the chunk is loaded. If it is not, it returns. If it is, it loops through the tiles in the chunk and sets the tile to -1. It then removes the chunk from the `loaded_chunks` dictionary.

`-1` is the tile id for an empty tile. This will allow the chunk to be garbage collected, and free up memory.

### Generate Chunks Around the Player

Now, we want to write code that generates chunks around the player. We will do this by getting the player's chunk position, and generating chunks around the player's chunk position.

We use a signal to get the player's position so we only generate chunks when needed, as chunks are expensive to generate. We also use a signal so we can generate chunks when the player moves, and when the player teleports.

Add the following code to the `world_generation.gd` script:

```gdscript
func _ready():
    # ... previous code ...
    Globals.player_moved.connect(_on_player_moved)

func _on_player_moved(player_position):
	var player_to_tile = local_to_map(to_local(player_position))
	var chunk_position = Vector2i(floori(player_to_tile.x / Globals.CHUNK_SIZE.x), floori(player_to_tile.y / Globals.CHUNK_SIZE.y))
	var start_chunk = chunk_position - Vector2i(Globals.VIEW_DISTANCE, Globals.VIEW_DISTANCE)
	var end_chunk = chunk_position + Vector2i(Globals.VIEW_DISTANCE, Globals.VIEW_DISTANCE)
	for loaded_chunk in loaded_chunks.keys():
		if loaded_chunk.x < start_chunk.x or loaded_chunk.x > end_chunk.x or loaded_chunk.y < start_chunk.y or loaded_chunk.y > end_chunk.y:
			call_deferred("unload_chunk", loaded_chunk)
	for x in range(start_chunk.x, end_chunk.x + 1):
		for y in range(start_chunk.y, end_chunk.y + 1):
			call_deferred("load_chunk", Vector2i(x, y))
```

This function takes the player's position and converts it to a tile position. It then converts the tile position to a chunk position. This allows us to generate a list of chunks around the player. We then loop through the chunks that are loaded, and unload chunks that are too far away from the player. We then loop through the chunks that are not loaded, and load chunks that are close to the player.

We use `call_deferred` to call the `load_chunk` and `unload_chunk` functions. This allows us to call the functions at the end of the frame, instead of immediately. This allows us to avoid a bug where the player's position is not updated before the chunks are generated. It also offloads the chunk generation to only run when it has a chance to run and not slowing down the game.

### Disable the Old Generation Code

Now that we have the new generation code, we can disable the old generation code. Add the following code to the `world_generation.gd` script:

```gdscript
func _ready():
    # ... previous code ...
	#for x in range(-100, 100):
		#for y in range(-100, 100):
			#select_tile(Vector2(x, y))
```

## Combine the Player and the World Generation

> **_NOTE:_**  This block is only necessary for testing, please put the `TileMap` and `player` wherever you need them in your game.

Now that we have the `player` node and the `TileMap` node, we need to combine the two. We will do this by creating a new scene that contains both the `player` node and the `TileMap` node.

Create a new scene by clicking the `+` in the middle of the screen and then clicking `2D Scene`. Go ahead and name the scene `World`. Now, drag the `player` scene from the `FileSystem` panel into the `World` scene. Now, drag the `TileMap` scene from the `FileSystem` panel into the `World` scene.

## Final Product

Now, when you run the game, you should see chunks being generated around the player. As the player moves, chunks will be generated and unloaded. This allows us to have an infinite world, without generating the entire world at once.

![Chunk Generation with Player Tracking](images/chunk.gif)

This is with a `TileMap` scale of 0.25.
# Signal Busses 

```
Godot Version: 4.2.1
Tested on: Janurary 29, 2024
Created by Avery Fernandez
```

Godot uses a strong system of signals to communicate between nodes. This system is already super strong, but becomes limited when trying to communicate between nodes that are not directly connected. For example, if we have a `Player` node and a `World` node, and we want the `Player` node to communicate with the `World` node, we would have to connect the `Player` node to the `World` node. This can become a problem if the `Player` node is not a direct child of the `World` node. We can solve this problem by using signal busses.


## Create the Signal Bus

Signal busses can be established by creating an autoload script. Autoload scripts are scripts that are always running, and can be accessed from any other script. We will use this to create a signal bus.

First, we need to create the autoload script. I like to keep all my autoload scripts in a folder called `Utils` in the `res://` directory. Create a new script in the `res://Utils/` directory by right clicking on the `res://Utils/` directory, clicking `New Script`. You can name the script whatever you want, but I like to name it `Signal_Bus.gd`.

### Autoload the Script

We now need to autoload the script. To do this, click on the `Project` menu at the top of the screen and click `Project Settings`. In the `Project Settings` window, click on the `AutoLoad` tab. Click the `Folder` button, and navigate to your script (`res://Utils/Signal_Bus.gd`). Click `Open`. Now, you should see your script path in the `Path` column. Godot will automatically give it a `Node Name` based on the name of the script, but you can change it if you want. I like to keep it the same as the script name. Now you can click `Add` button to add the script to the autoload list. Finally, make sure the `Enable` checkbox is checked for your script.

![Autoloaded Script](images/project_settings.png)

## Uses of Signal Busses

Signal Bus scripts have a variety of uses. Here are a few examples:
* **Global Events**: You can use signal busses to create global events that can be triggered from anywhere in your game.
* **Variable Sharing**: You can use signal busses to share variables and their changes between different nodes.
* **Cross-Scene Communication**: You can use signal busses to communicate between nodes in different scenes.

## Global Events Example

Here is an example of how you can use a signal bus to create a global event system. In this example, we will create a signal bus that can be used to trigger a global event called `game_over`.

### Create the Signal Bus

Create a new script in your `res://Utils/` directory called `Signal_Bus.gd`. In this script, add the following code:

```gdscript
extends Node

signal game_over

func trigger_game_over():
    emit_signal("game_over")
```

### Using the Signal Bus

Now that we have created the signal bus, we can use it in any other script to trigger the `game_over` event. Here is an example of how you can use the signal bus in another script:

```gdscript
extends Node

func _on_Player_death():
    Signal_Bus.trigger_game_over()
```

In this example, when the `Player` node dies, it triggers the `game_over` event on the signal bus. This event can be connected to any other node in your game that needs to know when the game is over.

## Variable Sharing Example

Here is an example of how you can use a signal bus to share variables between different nodes. In this example, we will create a signal bus that can be used to share a variable called `score`.

### Create the Signal Bus

Add the following code to your `Signal_Bus.gd` script:
```gdscript
extends Node

var score = 0

signal score_changed(new_score)

func set_score(new_score):
    score = new_score
    emit_signal("score_changed", new_score)
```

### Using the Signal Bus

Now that we have created the signal bus, we can use it in any other script to set and get the `score` variable. Here is an example of how you can use the signal bus in another script:

```gdscript
extends Node

func _on_Coin_collected():
    Signal_Bus.set_score(Signal_Bus.score + 1)
```

In this example, when the `Coin` node is collected, it increments the `score` variable on the signal bus. This change in the `score` variable is then broadcasted to any other node that is connected to the `score_changed` signal.

## Cross-Scene Communication Example

Here is an example of how you can use a signal bus to communicate between nodes in different scenes. In this example, we will create a signal bus that can be used to communicate between a `Player` node in one scene and a `World` node in another scene.

### Create the Signal Bus

Add the following code to your `Signal_Bus.gd` script:
```gdscript
extends Node

signal player_jump

func player_jump():
    emit_signal("player_jump")
```

### Using the Signal Bus

Now that we have created the signal bus, we can use it in any other script to trigger the `player_jump` event. Here is an example of how you can use the signal bus in another script:

```gdscript
extends Node

func _on_Player_jump():
    Signal_Bus.player_jump()
```

In this example, when the `Player` node jumps, it triggers the `player_jump` event on the signal bus. This event can be connected to any other node in your game that needs to know when the player jumps.

## Conclusion

Signal busses are a powerful tool in Godot that can be used to communicate between nodes that are not directly connected. By creating an autoload script that acts as a signal bus, you can create a global event system, share variables between nodes, and communicate between nodes in different scenes. Signal busses are a great way to keep your code organized and decoupled, and can help you create more flexible and maintainable games.
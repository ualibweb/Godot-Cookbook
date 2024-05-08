# External Python Access

```
Godot Version: 4.2.2
Tested on: May 8, 2024
Created by Avery Fernandez
```

While Godot has its own scripting language, GDScript, it is possible to use Python as an external scripting language. This can be useful if you are already familiar with Python or if you want to use Python libraries that are not available in GDScript. This tutorial will show you how to set up an external Python script that can interact with your Godot game.

## Setup the environment

Before you can start using Python with Godot, you need to set up an environment that can run Python scripts. We will setup a `Python Virtual Environment` for this purpose.

1. Open a terminal or command prompt.
2. Navigate to the directory where your Godot project is located.
3. Create a new Python virtual environment by running the following command:
```bash
python3 -m venv .venv
```

This will create a new directory called `.venv` in your project directory that contains a Python virtual environment.

## Using the Python script


### Create a Python script

Now that you have set up the Python virtual environment, you can create a Python script that you can run from Godot.

1. Create a new Python script in your project directory. You can name it `script.py`.
2. In the Python script, write the following code:
```python
print("Hello from Python!")
```

Now that we have a python script, we can run it from Godot using the `OS` class.

### Setup the Scene in Godot

1. Open your Godot project.
2. Click on the `+` icon in the FileSystem dock to add a new scene.
3. Create a new `User Interface` scene.
    * You can name it `Python Runner`.
    * This does not need to be a `User Interface` scene, this is just for demonstration purposes.
4. Add a `Button` node as a child of the `User Interface` node.
    * You can name it `Run Python Script`.
    * You can also add text to the button like `Run Python`.
5. Add a new script to the `Python Runner` scene.
6. We will connect the `pressed` signal of the `Button` node to the script.
    * This should be called something like `_on_run_python_script_pressed()`.

### Run the Python script from Godot

1. In the script for the `Python Runner` scene, write the following code:
```gdscript
extends Control

@onready var base_path = ProjectSettings.globalize_path("res://")

func _on_run_python_script_pressed():
    # Setup arguments for the Python script
    var python_executable = base_path + ".venv/bin/python" # For Windows use .venv/Scripts/python
    var python_file = base_path + "script.py"
    var python_arguments = []
    var os_arguments = [python_file] + python_arguments

    var output_array = []
    var error_response = OS.execute(python_executable, os_arguments, output_array, true, false)

    if error_response == OK:
        print("Python script executed successfully.")
        print("Output:")
        for line in output_array:
            print(line)
    else:
        print("Error executing Python script.")
        print("Error code:", error_response)
```

This uses the `OS.execute` method to run the Python script from Godot. The `python_executable` variable should point to the Python executable in the virtual environment. The `python_file` variable should point to the Python script you want to run.

`OS.execute` takes the following arguments:
* `python_executable`: The path to the Python executable.
* `os_arguments`: The file path of the Python script and any additional arguments.
* `output_array`: An array that will store the output of the Python script.
* `read_stdout`: A boolean that determines whether to read the standard output of the Python script.
* `read_stderr`: A boolean that determines whether to read the standard error of the Python script.

If you end up wanting to pass arguments to the Python script, you can add them to the `python_arguments` list.

When you run the `Python Runner` scene and click the `Run Python Script` button, the Python script will be executed, and the output will be printed to the Godot console.

## Advanced Usage

### Programmatically Create Python Environment

Go ahead and add another button to the `Python Runner` scene. This button will be used to create the Python virtual environment programmatically.

1. Add a new `Button` node as a child of the `User Interface` node.
    * You can name it `Create Python Environment`.
    * You can also add text to the button like `Create Python Environment`.
2. Connect the `pressed` signal of the `Button` node to a new script method called `_on_create_python_environment_pressed()`.
3. In the script for the `Python Runner` scene, write the following code:
```gdscript
func _on_create_python_environment_pressed():
    # Since we are creating the virtual environment programmatically, we need it to work on all platforms
    var executables = ["python3", "python"]
    var arguments = ["-m", "venv", ".venv"]
    var output_array = []
    var error_response = OK
    
    # Test each executable to see if it can create the virtual environment
    for executable in executables:
        error_response = OS.execute(executable, arguments, output_array, true, false)
        if error_response == OK:
            print("Virtual environment created successfully.")
            break
    if error_response != OK:
        print("Error creating virtual environment.")
        print("Error code:", error_response)
```

This code will attempt to create the Python virtual environment using the `python3` and `python` executables. If the virtual environment is created successfully, the output will be printed to the Godot console.

### Programmatically Install Python Packages

Go ahead and add another button to the `Python Runner` scene. This button will be used to install Python packages programmatically.

1. Add a new `Button` node as a child of the `User Interface` node.
    * You can name it `Install Python Packages`.
    * You can also add text to the button like `Install Python Packages`.
2. Connect the `pressed` signal of the `Button` node to a new script method called `_on_install_python_packages_pressed()`.
3. In the script for the `Python Runner` scene, write the following code:
```gdscript
func _on_install_python_packages_pressed():
    var os_name = OS.get_name()
    var pip_path = base_path
    if os_name == "Windows":
        pip_path += ".venv/Scripts/pip"
    else:
        pip_path += ".venv/bin/pip"
    
    var packages = ["requests", "numpy"] # Add any packages you want to install
    var arguments = ["install"] + packages
    
    var output_array = [] 
    var error_response = OS.execute(pip_path, arguments, output_array, true, false)

    if error_response == OK:
        print("Packages installed successfully.")
        print("Output:")
        for line in output_array:
            print(line)
    else:
        print("Error installing packages.")
        print("Error code:", error_response)

### Better Run Python Script

Now that we have the ability to create a virtual environment and install packages, we can improve the `Run Python Script` button to automatically update the path based on the operating system.

1. In the script for the `Python Runner` scene, update the `_on_run_python_script_pressed()` method to the following:
```gdscript
func _on_run_python_script_pressed():
    var os_name = OS.get_name()
    var python_executable = base_path
    if os_name == "Windows":
        python_executable += ".venv/Scripts/python"
    else:
        python_executable += ".venv/bin/python"
    
    var python_file = base_path + "script.py"
    var python_arguments = []
    var os_arguments = [python_file] + python_arguments

    var output_array = []
    var error_response = OS.execute(python_executable, os_arguments, output_array, true, false)

    if error_response == OK:
        print("Python script executed successfully.")
        print("Output:")
        for line in output_array:
            print(line)
    else:
        print("Error executing Python script.")
        print("Error code:", error_response)
```

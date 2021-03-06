# Replace ROS2 Communication Channels with Lingua Franca

## Overview

In this example, the communication channel of ROS2 Python package is replaced with a Lingua Franca communication channel. The ROS2 package contains a publisher class and a subscriber class. For each ROS Python class, a corresponding Lingua Franca Reactor is created to take over the input or output ports. All LF reactors are imported into `Main.lf` to build a federation. Follow the instructions below to run the LF program. 

## Instructions 

Make sure the working directory is `examples-lingua-franca/Python/src/ROS/PythonMigration`

### Compile ROS2 package
```
source /opt/ros/foxy/setup.bash
cd py_pubsub && colcon build
cd .. # Change back to original working directory
```

### Compile Lingua Franca package
```
. py_pubsub/install/setup.bash
lfc -c lf-python/Main.lf # -c clears previous builds
```

### Run Lingua Franca program
```
source /opt/ros/foxy/setup.bash
source ./py_pubsub/install/setup.bash
lf-python/bin/Main
```

## Migration Details
### Source setup file
Before compile the LF program, we always have to source the `install/setup.sh` file generated by `colcon build`, so the python package is added to the import search path for LF program.

### Import Python Class
Unlike target languages C and C++, Python does not require cmake files. To import a ROS Python class into Lingua Franca, a common import syntax is sufficient. Specifically, we need to locate the package name, the name of the file that contains the node we want to migrate, and the name of the class that inherits the ROS `Node` class. The import code in LF preamble section would be: 

```
from [package_name].[file_name] import [class_name]
```
### Import Dependencies
In addition to importing the class we want to migrate, we also need to import all the dependencies of that class, which are usually in the same file in which the class is defined. 

For example, the dependencies of `MinimalPublisher` is right above the class definition, and we need to copy them into the LF preamble section as well.

```
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class MinimalPublisher(Node):
    ...
```
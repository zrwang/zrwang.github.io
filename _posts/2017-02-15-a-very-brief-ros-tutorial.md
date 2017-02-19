---
layout: post
title: A Very Brief ROS Tutorial
---

## 0.0 Installing Debian-based Linux distributions like Ubuntu.
Theoretically, you can install ROS under any Debian-based Linux distributions. However, this tutorial is tested under [Ubuntu 14.04 LTS](http://releases.ubuntu.com/14.04/).

## 0.1 Installing ROS
Here is the documentation for installation of [Indigo](http://wiki.ros.org/indigo/Installation/Ubuntu) and [Jade](http://wiki.ros.org/jade/Installation/Ubuntu). You can also install the latest version, but the codes in this tutorial are only tested with **Indigo** and **Jade**.

If you encounter any issues with adding the key, please use the following command:
```Shell
wget https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -O - | sudo apt-key add -
```

## 0.1 Installing Catkin Command Line Tools
Here is the documentation for [catkin_tools](https://catkin-tools.readthedocs.io/en/latest/index.html).

## 1. Hello World
Open a terminal (ctrl+alt+t). Copy and paste the following codes into your terminal. Please modify the maintainer's name and email, and also the author's name and email.
```Shell
# create catkin workspace
cd ~
mkdir -p catkin_ws/src
# initialize catkin workspace
cd ~/catkin_ws
catkin init --workspace .
# create ros package (hello_world)
cd src
catkin create pkg --rosdistro indigo -c roscpp rospy -l BSD -m maintainer_name maintainer_email@example.com -a author_name author_email@example.com hello_world
＃ change working directory to source folder under package folder
cd hello_world/src
```
Open your favorite editor (e.g.,. Emacs) to edit the package.xml and CMakeLists.txt files if you did something wrong when you created the package, or you want to add more information.

Create **hello_world_node.cpp** file with following contents.
```C++
// Include the ROS C++ APIs
#include <ros/ros.h>

// Standard C++ entry point
int main(int argc, char** argv) {
  // Announce this program to the ROS master as a "node" called "hello_world_node"
  ros::init(argc, argv, "hello_world_node");
  // Start the node resource managers (communication, time, etc)
  ros::start();
  // Broadcast a simple log message
  ROS_INFO_STREAM("Hello, world!");
  // Process ROS callbacks until receiving a SIGINT (ctrl-c)
  ros::spin();
  // Stop the node's resources
  ros::shutdown();
  // Exit tranquilly
  return 0;
}
```
Modify corresponding parts in the package.xml and CMakeLists.txt so that the catkin tools can help you to compile the executables correctly.

You can specify additional locations of header files, declare C++ executables and anything else you want to control in the **Build** part of the **CMakeLists.txt** file. For example, in this example, we only need to declare one C++ executable named hello_world_node and link the catkin library to the executable.
```CMake
###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
# include_directories(include)
include_directories(
  ${catkin_INCLUDE_DIRS}
)

## Declare a C++ library
# add_library(hello_world
#   src/${PROJECT_NAME}/hello_world.cpp
# )

## Add cmake target dependencies of the library
## as an example, code may need to be generated before libraries
## either from message generation or dynamic reconfigure
# add_dependencies(hello_world ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Declare a C++ executable
add_executable(hello_world_node src/hello_world_node.cpp)

## Add cmake target dependencies of the executable
## same as for the library above
# add_dependencies(hello_world_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Specify libraries to link a library or executable target against
target_link_libraries(hello_world_node
  ${catkin_LIBRARIES}
)
```
It is not necessary to modify anything in the package.xml in this example. However, if you have multiple packages and some of the packages have sort of dependents on the other packages, you can modify the package.xml file to tell the catkin tools the dependent relationship of all the packages.

You should have a similar architecture tree as follows.
```Shell
my_catkin_ws/
└── src
    └── hello_world
        ├── CMakeLists.txt [modified]
        ├── include
        │   └── hello_world
        ├── package.xml [modified]
        └── src
            └── hello_world_node.cpp [created]
``` 

Now you are ready to compile the ROS package by catkin tools using the following command.
```Shell
# add ROS environment variables to your current bash session (modify indigo to jade if you are using jade)
source /opt/ros/indigo/setup.bash
# build the package
catkin build hello_world
```

After compiling the package, you can launch the ROS node. Please do remember to start a the **roscore** before executing the node.
```Shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# run the node (executable)
rosrun hello_world hello_world_node
```

By running the node, you should be able to observe the following results in your terminal.
```Shell
[ INFO] [1475573239.386991148]: Hello, world!
```

## 2. Turtlesim
Install some dependents.
```Shell
sudo apt-get install qt5-default
```

Clone the package from GitHub.
```Shell
# change directory to the package src folder
cd ~/catkin_ws/src
# clone the repository from github, and copy the specific package to the package source directory
git clone https://github.com/ros/ros_tutorials.git
cp -r ros_tutorials/turtlesim/ .
# delete unnecessary files
rm -rf ros_tutorials
```

Build the package.
```Shell
# add ROS environment variables to your current bash session (modify indigo to jade if you are using jade)
source /opt/ros/indigo/setup.bash
# build the package
catkin build turtlesim
```

Open three terminals, and add catkin workspace environment variables to each of them.

Terminal 1:
```Shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# launch roscore
roscore
```

Terminal 2:
```Shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# run graphic turtle simulator node
rosrun turtlesim turtlesim_node
```

Terminal 3:
```Shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# run teleoperation keyboard node
rosrun turtlesim turtle_teleop_key
```

Enjoy playing with the lovely little turtle.

Using the following tools can help you to have a better understanding of the topology of the running ROS nodes.
```Shell
rosnode [ping|list|info]
rostopic [echo|pub|hz|info|list|type]
rosrun rqt_graph rqt_graph
```

## 3. Video Pipeline
Install some dependents
```Shell
sudo apt-get install libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev
```

Clone the package from GitHub.
```Shell
# add ROS environment variables to your current bash session (modify indigo to jade if you are using jade)
source /opt/ros/indigo/setup.bash
# clone packages from github
cd ~/catkin_ws/src
git clone https://github.com/ros-drivers/gscam.git
git clone https://github.com/zrwang/hauppauge.git
# build the package
catkin build hauppauge
```

Launch the video pipeline.
```Shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# launch video pipeline
roslaunch hauppauge mono.launch
```

You can observe what this launch file does by using **rqt_image_view** package in another terminal console.
```Shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# run rqt_image_view
rosrun rqt_image_view rqt_image_view
```

Here is a very useful [tutorial](https://github.com/jhu-dvrk/dvrk-ros/blob/feature-ecm/dvrk_robot/video.md) by [Anton Deguet](https://github.com/adeguet1) about streaming video from an endoscope.

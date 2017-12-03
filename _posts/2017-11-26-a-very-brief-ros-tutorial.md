---
layout: post
title: A Very Brief ROS Tutorial
description: This is a brief tutorial showing you how to properly install and use ROS. The tutorial has three examples, which cover the most basic stuffs as well as working principles and philolophies.
---

## 0.0 Installing Debian-based Linux distributions like Ubuntu.
Theoretically, you can install ROS under any Debian-based Linux distributions. However, this tutorial is tested under [Ubuntu 16.04 LTS](http://releases.ubuntu.com/16.04/){:target="_blank"}.

## 0.1 Installing ROS
Here is the documentation for installation of [Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu) and [Lunar](http://wiki.ros.org/lunar/Installation/Ubuntu){:target="_blank"}. The codes in this tutorial are only tested with **Kinetic**.

## 0.1 Installing Catkin Command Line Tools
Here is the documentation for [catkin_tools](https://catkin-tools.readthedocs.io/en/latest/index.html){:target="_blank"}.

## 1. Hello World
Open a terminal (ctrl+alt+t). Copy and paste the following codes into your terminal. Please modify the maintainer's name and email, and also the author's name and email.

```shell
# create catkin workspace
cd ~
mkdir -p catkin_ws/src
# initialize catkin workspace
cd ~/catkin_ws
catkin init --workspace .
# create ros package (hello_world)
cd src
catkin create pkg --rosdistro kinetic -c roscpp rospy -l BSD -m maintainer_name maintainer_email@example.com -a author_name author_email@example.com hello_world
＃ change working directory to source folder under package folder
cd hello_world/src
```

Open your favorite editor (e.g., Emacs, Sublime Text 3) to edit the package.xml and CMakeLists.txt files if you did something wrong when you created the package, or you want to add more information.

Create **hello_world_node.cpp** file with following contents.

```cpp
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

```cmake
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

```shell
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

```shell
# add ROS environment variables to your current bash session (modify kinetic to lunar if you are using lunar)
source /opt/ros/kinetic/setup.bash
# since the above command will be used quite frequently, we can add an alias into ~/.bashrc as follows
# echo "alias souros='source /opt/ros/kinetic/setup.bash'" >> ~/.bashrc
# then we can simply run "souros"
# build the package
catkin build hello_world
```

After compiling the package, you can launch the ROS node. Please do remember to start a the **roscore** before executing the node.

```shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# run the node (executable)
rosrun hello_world hello_world_node
```

By running the node, you should be able to observe the following results in your terminal.

```shell
[ INFO] [1475573239.386991148]: Hello, world!
```

## 2. Turtlesim
Install some dependents.

```shell
sudo apt-get install qt5-default
```

Clone the package from GitHub.

```shell
# change directory to the package src folder
cd ~/catkin_ws/src
# clone the repository from github, and copy the specific package to the package source directory
git clone https://github.com/ros/ros_tutorials.git
cp -r ros_tutorials/turtlesim/ .
# delete unnecessary files
rm -rf ros_tutorials
```

Build the package.

```shell
# add ROS environment variables to your current bash session (modify kinetic to jade if you are using lunar)
source /opt/ros/kinetic/setup.bash
# build the package
catkin build turtlesim
```

Open three terminals, and add catkin workspace environment variables to each of them.

Terminal 1:

```shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# launch roscore
roscore
```

Terminal 2:

```shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# run graphic turtle simulator node
rosrun turtlesim turtlesim_node
```

Terminal 3:

```shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# run teleoperation keyboard node
rosrun turtlesim turtle_teleop_key
```

Enjoy playing with the lovely little turtle.

Using the following tools can help you to have a better understanding of the topology of the running ROS nodes.

```shell
rosnode [ping|list|info]
rostopic [echo|pub|hz|info|list|type]
rosrun rqt_graph rqt_graph
```

## 3. Video Pipeline
Install some dependents

```shell
sudo apt-get install libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev
```

Clone the package from GitHub.

```shell
# add ROS environment variables to your current bash session (modify kinetic to jade if you are using lunar)
source /opt/ros/kinetic/setup.bash
# clone packages from github
cd ~/catkin_ws/src
git clone https://github.com/ros-drivers/gscam.git
git clone https://github.com/zrwang/hauppauge.git
# build the package
catkin build hauppauge
```

Launch the video pipeline.

```shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# launch video pipeline
roslaunch hauppauge mono.launch
```

You can observe what this launch file does by using **rqt_image_view** package in another terminal console.

```shell
# add catkin workspace environment variables to your current bash session
source ~/catkin_ws/devel/setup.bash
# run rqt_image_view
rosrun rqt_image_view rqt_image_view
```

Here is a very useful [tutorial](https://github.com/jhu-dvrk/dvrk-ros/blob/feature-ecm/dvrk_robot/video.md){:target="_blank"} by [Anton Deguet](https://github.com/adeguet1){:target="_blank"} about streaming video from an endoscope.

# Acknowledgments

I got the C++ code of the first example (Hello World) from this elegant [tutorial](http://jbohren.com/articles/roscpp-hello-world/){:target="_blank"} by [Jonathan Bohren](http://jbohren.com/){:target="_blank"}.

I borrowed the idea of the second example (Turtlesim) from ROS official [tutorial](http://wiki.ros.org/ROS/Tutorials/UnderstandingTopics){:target="_blank"}.

The hauppauge ROS package in the third example (Video Pipeline) is created by [Simon Leonard](http://www.cs.jhu.edu/~sleonard/){:target="_blank"}

Thanks for reading!

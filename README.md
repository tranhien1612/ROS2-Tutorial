# ROS2 Tutorial

## Running Executables
```
sudo apt update
sudo apt install ros-humble-turtlesim
```
See list of packages
```
ros2 pkg list
```
See list of <packages, executables>
```
ros2 pkg executables
```
See list of executables from turtlesim package
```
ros2 pkg executables turtlesim
```
Where is turtlesim?
```
cd /opt/ros/humble
code .
Ctrl+P, turtlesim
```
Shoule see `turtlesim` is located in
```
share/ament_index/resource_index/packages
```
Run turtlesim. Do `ros2 run -h` so see options
```
ros2 run <package_name> <executable_name>
ros2 run turtlesim turtlesim_node
```
In other terminal, run the teleop node
```
ros2 run turtlesim turtle_teleop_key
```

## ROS2 Node Commands
See ros2 node help
```
ros2 node -h
```
List current nodes running. Right now there should be nothing
```
ros2 node list
```
Run a node
```
ros2 run turtlesim turtlesim_node
```
Now list the nodes again and you should see `/turtlesim`
```
ros2 node list
```
See node info
```
ros2 node info <node_name>
ros2 node info /turtlesim
```

## ROS Topic
A topic is one way data is moved between nodes, where 1 or more publisher nodes can connect to 1 or more subscriber nodes.

Open new terminal and run
```
ros2 run <package_name> <executable>
ros2 run turtlesim turtlesim_node
```
Open new terminal and run. Move the turtle anywhere (needed for the rqt graph to show connection later)
```
ros2 run turtlesim turtle_teleop_key
```
See list of topics
```
ros2 topic list
# See Details
ros2 topic list -t
```
Start rqt graph. Uncheck hide boxes to see hidden topics
```
rqt_graph
```
See topic output. Move with teleop to see output after running command.
```
ros2 topic echo <topic_name>
ros2 topic echo /turtle1/cmd_vel
```
View topic info
```
ros2 topic info <topic_name>
ros2 topic info /turtle1/cmd_vel
```
See interface definition
```
ros2 interface show <type>
ros2 interface show geometry_msgs/msg/Twist
```
Publish data to a topic ("--once" means publish once and exit)
```
ros2 topic pub <topic_name> <msg_type> '<args>'
ros2 topic pub --once /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x:2.0, y:0.0, z:0.0}, angular: {x:0.0, y:0.0, z:1.8}}"
```
Publish data to a topic with rate 1 hz
```
ros2 topic pub --rate 1 /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x:2.0, y:0.0, z:0.0}, angular: {x:0.0, y:0.0, z:1.8}}"
```
Echo the pose topic. Go to the rqt graph and refresh
```
ros2 topic echo /turtle1/pose
```
View topic freq
```
ros2 topic hz <topic_name>
ros2 topic hz /turtle1/pose
```

## ROS Services
Services are used to communicate between nodes using a client-server model, where the server respondes when the client make a request.

In a new terminal, run
```
ros2 run turtlesim turtlesim_node
```
In another terminal, run
```
ros2 run turtlesim turtle_teleop_key
```
List all service names
```
ros2 service list
# See service type
ros2 service list -t
```
View service type another way
```
ros2 service type <service_name>
ros2 service type /clear
```
Find service with specific type
```
ros2 service find <service_type>
ros2 service find std_srvs/srv/Empty
```
See interface
```
ros2 interface show <service_type>
ros2 interface show turtlesim/srv/Spawn
```
Calling a service
```
ros2 service call <service_name> <service>
# Clear drawing
ros2 service call /clear std_srvs/Empty
# Spawn turtle
ros2 service call /spawn turtlesim/srv/Spawn "{x:2, y:2, theta:0.2, name:''}"
```

## ROS Parameters
Parameters are values you can change inside a node.

In a new terminal, run
```
ros2 run turtlesim turtlesim_node
```
In another terminal, run
```
ros2 run turtlesim turtle_teleop_key
```
See list of parameters
```
ros2 param list
```
Get parameter value
```
ros2 param get <node_name> <parameter_name>
ros2 param get /turtlesim background_g
```
Set parameter value
```
ros2 param set <node_name> <parameter_name> <value>
ros2 param set /turtlesim background_g 255
```
Store param in yaml file
```
ros2 param dump /turtlesim > turtlesim.yaml
```
Load param from yaml file
```
ros2 param load /turtlesim turtlesim.yaml
```
Load param on startup
```
ros2 run <package_name> <executable_name> --ros-args --params-file <file_name>
ros2 run turtlesim turtlesim_node --ros-args --params-file turtlesim.yaml
```

## ROS Actions
Actions lets you communicate between nodes with a goal, feadback, and result in a client-server fashion

In a new terminal, run
```
ros2 run turtlesim turtlesim_node
```
In another terminal, run
```
ros2 run turtlesim turtle_teleop_key
```
In the telop window, try the following and observe
a. Press one of the letters 'G' and see the status when complete
b. Press one of the letters and cancel early with 'F'
c. Press one of the letters 'G' and before it finishes press another 'F'

See the action clients in node info on the bottom
```
ros2 node info /teleop_turtle
```
See all actions
```
ros2 action list
# List with type
ros2 action list -t
```
See action info
```
ros2 action info <action_name>
ros2 action info /turtle1/rotate_absolute
```
See data type for action
```
ros2 interface show turtlesim/action/RotateAbsolute
```
Send action goal
```
ros2 action send_goal <action_name> <action_type> <goal>
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta:1.57}"
```

## Build Packages with Colcon
A package contains your source, CmakeList.txt, headers, and package.xml (meta info) for your ROS programs. Colcon is the tool to build your package

Install colcon
```
sudo apt install python3-colcon-common-extensions
```
Create workspace and navigate to workspace
```
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
```
Add some source code to test building
```
git clone https://github.com/ros/ros_tutorials.git -b humble
```
Setup colcon tab completion
```
echo "source /usr/share/colcon_argcomplete/hook/colcon-argcomplete.bash" >> ~/.bashrc
```
Build with colcon from `~/ros2_ws`. The `--symlink-install` creates symbolic links (symlinks) to the original files in the source or build directories instead of making copies
```
colcon build --symlink-install
```
See the folders that were created (build, install, log)
```
ls
```
Source overlay from `~/ros2_ws`
```
source install/local_setup.bash
```
Run node
```
ros2 run turtlesim turtlesim_node
```
To verify you are modifying your package and not the previous one, let's edit the name of window. Hit `Ctrl+P` and search `turtle_frame.cpp`. Modify `line 52` to the following
```
setWindowTitle("KevinTurtleSim")
```
Rebuild, source, then run. Should see the name change
```
colcon build --symlink-install
source install/local_setup.bash
ros2 run turtlesim turtlesim_node
```

## Create Your Own Package
Recall a package contains your source, CmakeList.txt, headers, and package.xml (meta info) for your ROS programs. Here we will create our own package from scratch

Create package from `~/ros2_ws/src`
```
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
ros2 pkg create --build-type ament_cmake --node-name <node_name> <package_name>
ros2 pkg create --build-type ament_cmake --node-name my_node my_package
```
Check out the files and folders that it made. Open files and see what's inside.
```
include/my_package
src/my_node.cpp
CMakeLists.txt
package.xml
```
Build that package from `~/ros2_ws`
```
colcon build --packages-select my_package
```
Source
```
source install/local_setup.bash
```
Run node
```
ros2 run my_package my_node
```

## Publisher and Subscriber C++
In the ROS tutorial, we will create pub and sub package in C++

Install CMake extension to view CMakeLists.txt files with syntax coloring.

Create package from `~/ros2_ws/src`. We will make a `cpp_pubsub` package folder with the following generated `include`, `src`, `CMakeLists.txt`, and `package.xml`
```
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_cmake cpp_pubsub
```
In `cpp_pubsub/src`, create 2 file `publisher_member_function.cpp`, `subscriber_member_function.cpp`

```
publisher_member_function.cpp
#include <chrono>
#include <functional>
#include <memory>
#include <string>
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"
using namespace std::chrono_literals;

class MinimalPublisher : public rclcpp::Node
{
    public:
    MinimalPublisher() : Node("minimal_publisher"), count_(0)
    {
        publisher_ = this->create_publisher<std_msgs::msg::String>("topic", 10);
        timer_ = this->create_wall_timer(500ms, std::bind(&MinimalPublisher::timer_callback, this));
    }

    private:
        void timer_callback(){
            auto message = std_msgs::msg::String();
            message.data = "Hello, world" + std::to_string(count_++);
            RCLCPP_INFO(this->get_logger(), "Publishing: '%s'", message.data.c_str());
            publisher_->publish(message);
        }
        rclcpp::TimerBase::SharedPtr timer_;
        rclcpp::Publisher<std_msgs::msg::String>::SharedPtr publisher_;
        size_t count_;
};

int main(int argc, char* argv[]){
    // Initializes ROS2 communication
    rclcpp::init(argc, argv);

    // Creates an instance of MinimalPublisher and enters a loop, keeping the node alive and responsive to callbacks
    rclcpp::spin(std::make_shared<MinimalPublisher>());
    rclcpp::shutdown();
    return 0;
}

```

```
subscriber_member_function.cpp
#include <memory>
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"
using std::placeholders::_1;

class MinimalSubscriber : public rclcpp::Node
{
    public: 
    MinimalSubscriber() : Node("minimal_subscriber"){
        subscription_ = this->create_subscription<std_msgs::msg::String>("topic", 10, std::bind(&MinimalSubscriber::topic_callback, this, _1));
    }
    private:
    void topic_callback(const std_msgs::msg::String &msg) const {
        RCLCPP_INFO(this->get_logger(), "I heard: '%s'", msg.data.c_str());
    }
    rclcpp::Subscription<std_msgs::msg::String>::SharedPtr subscription_;
};

int main(int argc, char* argv[]){
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<MinimalSubscriber>());
    rclcpp::shutdown();
    return 0;
}
```

Update `package.xml`
```
<depend>rclcpp</depend>
<depend>std_msgs</depend>
```

Update `CMakeLists.txt`
```
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)

add_executable(talker src/publisher_member_function.cpp)
ament_target_dependencies(talker rclcpp std_msgs)

add_executable(listener src/subscriber_member_function.cpp)
ament_target_dependencies(listener rclcpp std_msgs)

install(TARGETS
    talker
    listener
    DESTINATION lib/${PROJECT_NAME}
)
```

Check for missing dependencies from `~/ros2_ws`
```
rosdep install -i --from-path src --rosdistro humble -y
```

Build with colon from `~/ros2_ws`
```
colcon build --packages-select cpp_pubsub
```

In one terminal, run the talker from `~/ros2_ws`
```
cd ~/ros2_ws
source install/setup.bash
ros2 run cpp_pubsub talker
```

In another terminal, run the listener from `~/ros2_ws`
```
cd ~/ros2_ws
source install/setup.bash
ros2 run cpp_pubsub listener
```

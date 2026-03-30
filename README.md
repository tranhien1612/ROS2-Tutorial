
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

## Publisher and Subcriber CPP or python
```
cd ~/ros2_ws/src
# cpp
ros2 pkg create --build-type ament_cmake cpp_pubsub
# python 
ros2 pkg create --build-type ament_python py_pubsub
```

In `cpp_pubsub/src`, create 2 file `publisher_member_function.cpp`, `subscriber_member_function.cpp` for cpp:

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

Create `publisher_member_function.py` and `subscriber_member_function.py` in `py_pubsub/py_pubsub` for python:
```
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class MinimalPublisher(Node):

    def __init__(self):
        super().__init__('minimal_publisher')
        self.publisher_ = self.create_publisher(String, 'topic', 10)
        self.timer = self.create_timer(0.5, self.timer_callback)
        self.count = 0

    def timer_callback(self):
        msg = String()
        msg.data = f'Hello, world {self.count}'
        self.publisher_.publish(msg)
        self.get_logger().info(f'Publishing: "{msg.data}"')
        self.count += 1


def main(args=None):
    rclpy.init(args=args)
    node = MinimalPublisher()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

```
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class MinimalSubscriber(Node):

    def __init__(self):
        super().__init__('minimal_subscriber')
        self.subscription = self.create_subscription(
            String,
            'topic',
            self.listener_callback,
            10
        )

    def listener_callback(self, msg):
        self.get_logger().info(f'I heard: "{msg.data}"')


def main(args=None):
    rclpy.init(args=args)
    node = MinimalSubscriber()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

Add command `package.xml`:
```
<exec_depend>rclpy</exec_depend>
<exec_depend>std_msgs</exec_depend>
```

Add command `setup.py`:
```
entry_points={
    'console_scripts': [
        'talker = py_pubsub.publisher_member_function:main',
        'listener = py_pubsub.subscriber_member_function:main'
    ],
},
```
Check for missing dependencies from `~/ros2_ws`
```
rosdep install -i --from-path src --rosdistro humble -y
```

Build with colon from `~/ros2_ws`
```
# cpp
colcon build --packages-select cpp_pubsub
# python
colcon build --packages-select py_pubsub
```
In one terminal, run the talker from `~/ros2_ws`
```
cd ~/ros2_ws
source install/setup.bash
ros2 run py_pubsub talker
```

In another terminal, run the listener from `~/ros2_ws`
```
cd ~/ros2_ws
source install/setup.bash
ros2 run py_pubsub listener
```

## Service and Client C++ or python
```
cd ~/ros2_ws/src
# cpp
ros2 pkg create --build-type ament_cmake cpp_srvcli --dependencies rclcpp example_interfaces
# python
ros2 pkg create --build-type ament_python py_srvcli --dependencies rclpy example_interfaces
```

Create `add_two_ints_client.cpp` and `add_two_ints_server.cpp` in `cpp_srvcli/src` for cpp:
```
#include <chrono>
#include <memory>
#include "rclcpp/rclcpp.hpp"
#include "example_interfaces/srv/add_two_ints.hpp"

using namespace std::chrono_literals;

class AddTwoIntsClient : public rclcpp::Node
{
public:
    AddTwoIntsClient() : Node("add_two_ints_client")
    {
        client_ = this->create_client<example_interfaces::srv::AddTwoInts>("add_two_ints");

        while (!client_->wait_for_service(1s)) {
            RCLCPP_INFO(this->get_logger(), "Waiting for service...");
        }

        auto request = std::make_shared<example_interfaces::srv::AddTwoInts::Request>();
        request->a = 5;
        request->b = 3;

        auto result = client_->async_send_request(request,
            std::bind(&AddTwoIntsClient::response_callback, this, std::placeholders::_1));
    }

private:
    void response_callback(rclcpp::Client<example_interfaces::srv::AddTwoInts>::SharedFuture future)
    {
        auto response = future.get();
        RCLCPP_INFO(this->get_logger(), "Result: %ld", response->sum);
        rclcpp::shutdown();
    }

    rclcpp::Client<example_interfaces::srv::AddTwoInts>::SharedPtr client_;
};

int main(int argc, char * argv[])
{
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<AddTwoIntsClient>());
    return 0;
}
```

```
#include <memory>
#include "rclcpp/rclcpp.hpp"
#include "example_interfaces/srv/add_two_ints.hpp"

using std::placeholders::_1;
using std::placeholders::_2;

class AddTwoIntsServer : public rclcpp::Node
{
public:
    AddTwoIntsServer() : Node("add_two_ints_server")
    {
        service_ = this->create_service<example_interfaces::srv::AddTwoInts>(
            "add_two_ints",
            std::bind(&AddTwoIntsServer::handle_service, this, _1, _2)
        );
        RCLCPP_INFO(this->get_logger(), "Service ready");
    }

private:
    void handle_service(
        const std::shared_ptr<example_interfaces::srv::AddTwoInts::Request> request,
        std::shared_ptr<example_interfaces::srv::AddTwoInts::Response> response)
    {
        response->sum = request->a + request->b;
        RCLCPP_INFO(this->get_logger(), "Request: %ld + %ld", request->a, request->b);
    }

    rclcpp::Service<example_interfaces::srv::AddTwoInts>::SharedPtr service_;
};

int main(int argc, char * argv[])
{
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<AddTwoIntsServer>());
    rclcpp::shutdown();
    return 0;
}
```

Add command in `CMakeLists.txt` if using cpp:
```
add_executable(add_two_ints_server src/add_two_ints_server.cpp)
ament_target_dependencies(add_two_ints_server rclcpp example_interfaces)

add_executable(add_two_ints_client src/add_two_ints_client.cpp)
ament_target_dependencies(add_two_ints_client rclcpp example_interfaces)

install(TARGETS
  add_two_ints_server
  add_two_ints_client
  DESTINATION lib/${PROJECT_NAME}
)
```

Create `add_two_ints_client.py` and `add_two_ints_server.py` in `py_srvcli/py_srvcli` for python:
```
import sys
import rclpy
from rclpy.node import Node
from example_interfaces.srv import AddTwoInts

class AddTwoIntsClient(Node):

    def __init__(self):
        super().__init__('add_two_ints_client')
        self.client = self.create_client(AddTwoInts, 'add_two_ints')

        while not self.client.wait_for_service(timeout_sec=1.0):
            self.get_logger().info('Waiting for service...')

    def send_request(self, a, b):
        request = AddTwoInts.Request()
        request.a = a
        request.b = b
        return self.client.call_async(request)


def main(args=None):
    rclpy.init(args=args)

    node = AddTwoIntsClient()

    a = 5
    b = 3

    future = node.send_request(a, b)
    rclpy.spin_until_future_complete(node, future)

    if future.result() is not None:
        node.get_logger().info(f"Result: {future.result().sum}")
    else:
        node.get_logger().error("Service call failed")

    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

```
import rclpy
from rclpy.node import Node
from example_interfaces.srv import AddTwoInts

class AddTwoIntsServer(Node):

    def __init__(self):
        super().__init__('add_two_ints_server')
        self.srv = self.create_service(
            AddTwoInts,
            'add_two_ints',
            self.handle_add_two_ints
        )
        self.get_logger().info("Service ready")

    def handle_add_two_ints(self, request, response):
        response.sum = request.a + request.b
        self.get_logger().info(f"Incoming request: {request.a} + {request.b}")
        return response


def main(args=None):
    rclpy.init(args=args)
    node = AddTwoIntsServer()
    rclpy.spin(node)
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

Add command in `setup.py` if using python:
```
'console_scripts': [
    'add_server = py_srvcli.add_two_ints_server:main',
    'add_client = py_srvcli.add_two_ints_client:main',
],
```

Build
```
# cpp
colcon build --packages-select cpp_srvcli
# python
colcon build --packages-select py_srvcli
```

In a terminal, run Server:
```
# cpp
ros2 run cpp_srvcli add_two_ints_server
# python
ros2 run cpp_srvcli add_server
```
In another terminal, run Client:
```
# cpp
ros2 run cpp_srvcli add_two_ints_client
# python
ros2 run cpp_srvcli add_client
```

## Service with custom interface
```
cd ~/ros2_ws
ros2 pkg create --build-type ament_cmake tutorial_interfaces
cd tutorial_interfaces
mkdir srv msg
```
Create `Num.msg` and `Sphere.msg` in msg folder and `AddThreeInts.srv` in srv folder:
```
int64 num
```
```
geometry_msgs/Point center
float64 radius
```
```
int64 a
int64 b
int64 c
---
int64 sum
```
Add command in `CMakeLists.txt`:
```
find_package(geometry_msgs REQUIRED)
find_package(rosidl_default_generators REQUIRED)
rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/Num.msg"
  "msg/Sphere.msg"
  "srv/AddThreeInts.srv"
  DEPENDENCIES geometry_msgs
)
```
Add command in `package.xml`:
```
  <depend>geometry_msgs</depend>
  <buildtool_depend>ament_cmake</buildtool_depend>
  <buildtool_depend>rosidl_default_generators</buildtool_depend>
  <exec_depend>rosidl_default_runtime</exec_depend>
  <member_of_group>rosidl_interface_packages</member_of_group>
```

Build and test:
```
colcon build --packages-select tutorial_interfaces
source install/setup.bash
ros2 interface show tutorial_interfaces/msg/Num
ros2 interface show tutorial_interfaces/msg/Sphere
ros2 interface show tutorial_interfaces/srv/AddThreeInts
```

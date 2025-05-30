# Drone Simulation using Gazebo11
by el-jausyan

first think first before you do drone simulation in gazebo you need to donwload all dependencies need in ubuntu, first things fisrt you need to download this
1. ROS 2 Foxy
2. Mavros
3. Ardupilot
4. Ardupilot_gazebo
5. Mavlink / stlink
6. geographicslib
7. gazebo classic

<mark>How to simulate your drone in Gazebo classic</mark> <br>
Go to the terminal (Ctrl + Alt + T)

## Terminal 1
Run MAVProxy
```sh
cd ardupilot/ArduCopter
sim_vehicle.py -v ArduCopter -f gazebo-iris --console
```

## Terminal 2
Run Gazebo and load Iris Drone model
```sh
gazebo --verbose ~/ardupilot_gazebo/worlds/iris_arducopter_runway.world
```
Dont forget to check Connection status at console
![drone](s1.png)

## Terminal 3
run MAVROS Console
```sh
ros2 run mavros mavros_node
```
or
```sh
ros2 run mavros mavros_node --ros-args -p fcu_url:=udp://127.0.0.1:14550@14550
```
real drone
```sh
ros2 run mavros mavros_node --ros-args -p fcu_url:=serial:///dev/ttyACM0:57600
```
or
```sh
ros2 run mavros mavros_node --ros-args -p fcu_url:=serial:///dev/ttyUSB0:115200
```

![mavros](s2.png)

## check ros2 topic connection
```sh
ros2 topic list
```
## simulate drone
to simulate drone goto MAVProxy console and type the following command
```sh
mode guided
arm throttle
takeoff <altitude_in_meters>
rtl
land
```
![table of simulation](s3.png)

Guided Mode (auto/rtl/land)
```sh
ros2 service call /mavros/set_mode mavros_msgs/srv/SetMode "{custom_mode: 'guided'}"
```
Arming
```sh
ros2 service call /mavros/cmd/arming mavros_msgs/srv/CommandBool "{value: true}"
```
takeoff
```sh
ros2 service call /mavros/cmd/takeoff mavros_msgs/srv/CommandTOL "{altitude: 1}"
```
Forward
```sh
ros2 topic pub --once /mavros/setpoint_velocity/cmd_vel_unstamped geometry_msgs/msg/Twist "{linear: {x: 1.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```
Back
```sh
ros2 topic pub --once /mavros/setpoint_velocity/cmd_vel_unstamped geometry_msgs/msg/Twist "{linear: {x: -1.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```
Right
```sh
ros2 topic pub --once /mavros/setpoint_velocity/cmd_vel_unstamped geometry_msgs/msg/Twist "{linear: {x: 0.0, y: -1.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```
Left
```sh
ros2 topic pub --once /mavros/setpoint_velocity/cmd_vel_unstamped geometry_msgs/msg/Twist "{linear: {x: 0.0, y: 1.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```
Up
```sh
ros2 topic pub --once /mavros/setpoint_velocity/cmd_vel_unstamped geometry_msgs/msg/Twist "{linear: {x: 0.0, y: 0.0, z: 1.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```
Down
```sh
ros2 topic pub --once /mavros/setpoint_velocity/cmd_vel_unstamped geometry_msgs/msg/Twist "{linear: {x: 0.0, y: 0.0, z: -1.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```
Yaw Left
```sh
ros2 topic pub --once /mavros/setpoint_velocity/cmd_vel_unstamped geometry_msgs/msg/Twist "{linear: {x: 0.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.0}}"
```
Yaw Right
```sh
ros2 topic pub --once /mavros/setpoint_velocity/cmd_vel_unstamped geometry_msgs/msg/Twist "{linear: {x: 0.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: -1.0}}"
```
Landing
```sh
ros2 service call /mavros/cmd/land mavros_msgs/srv/CommandTOL "{min_pitch: 0.0, yaw: 0.0, latitude: 0.0, longitude: 0.0, altitude: 0.0}"
```
Stop movement
```sh
ros2 topic pub /mavros/setpoint_velocity/cmd_vel_unstamped geometry_msgs/msg/Twist "{linear: {x: 0.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```
Send 10 Hz command
```sh
ros2 topic pub -r 10 /mavros/setpoint_velocity/cmd_vel_unstamped geometry_msgs/msg/Twist "{linear: {x: 1.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```

move to
```sh
ros2 topic pub /mavros/setpoint_position/local geometry_msgs/msg/PoseStamped "{
    header: {
        stamp: {sec: 0, nanosec: 0},
        frame_id: 'map'
    },
    pose: {
        position: {x: 1.0, y: 1.0, z: 1.0},
        orientation: {x: 0.0, y: 0.0, z: 0.0, w: 1.0}
    }
}" --once
```
# How to build worksapce ROS-2 Foxy
This im gonna show you how to build Ros2 workspcae to build script and drone task
## 1 Building Folder
```sh
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build
source install/setup.bash
```
## 2 Make package
based own your package script usage
```sh
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_cmake drone_teleop --dependencies rclcpp geometry_msgs mavros_msgs std_msgs
```
go to folder
```sh
cd drone_teleop
```
## 3 Make the script
```sh
mkdir src
cd src
touch teleop_keyboard.cpp
```
   

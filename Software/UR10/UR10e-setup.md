# Prerequisites
* install Ubuntu 24.04 LTS [Download](https://ubuntu.com/download/desktop)
* install ROS 2 Jazzy, e.g. following [ROS2 Documentation](https://docs.ros.org/en/jazzy/Installation.html)

# Relevant Tutorials/Resources
* [Universal Robots ROS documentation](https://www.universal-robots.com/developer/communication-protocol/ros-and-ros2-driver/)

# Install Universal Robots ROS2 Driver
Following
* [UR ROS2 Driver github](https://github.com/UniversalRobots/Universal_Robots_ROS2_Driver)
* [UR ROS2 Driver documentation](https://docs.universal-robots.com/Universal_Robots_ROS2_Documentation/doc/ur_robot_driver/ur_robot_driver/doc/installation/installation.html)

## Install the driver
```
sudo apt-get install ros-${ROS_DISTRO}-ur
```

## Prepare robot and network connection
Only required for robot and PC directly connected to the robot
* [Robot setup](https://docs.universal-robots.com/Universal_Robots_ROS2_Documentation/doc/ur_client_library/doc/setup/robot_setup.html#robot-setup) Follow the PolyScope 5 version

* [Network Setup](https://docs.universal-robots.com/Universal_Robots_ROS2_Documentation/doc/ur_client_library/doc/setup/network_setup.html#network-setup)
* Download URCaps from [URCaps](https://github.com/UniversalRobots/Universal_Robots_ExternalControl_URCap/releases)

### Robot
```
IP address: 192.168.56.101
Subnet mask: 255.255.255.0
Default gateway: 192.168.56.1
Preferred DNS server: 192.168.56.1
Alternative DNS server: 0.0.0.0
```
### Remote PC
```
IPv4
Manual
Address: 192.168.56.1
Netmask: 255.255.255.0
```
## If there are issues with the setup above try:
### Robot
```
IP address: 192.168.57.101
Subnet mask: 255.255.255.0
Default gateway: 192.168.57.1
Preferred DNS server: 192.168.56.1
Alternative DNS server: 0.0.0.0
```
### Remote PC
```
IPv4
Manual
Address: 192.168.57.1
Netmask: 255.255.255.0
```

### Verify that the connection between robot and PC works
```
ping 192.168.56.101
```
should output
```
PING 192.168.56.101 (192.168.56.101) 56(84) bytes of data.
64 bytes from 192.168.56.101: icmp_seq=1 ttl=64 time=0.037 ms
```
# Extract calibration information
```
$ ros2 launch ur_calibration calibration_correction.launch.py \
robot_ip:=192.168.56.101 target_filename:="${HOME}/my_robot_calibration.yaml"
```
> [!NOTE]
> The robot must be powered on (can be idle) before executing this script.

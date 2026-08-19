following https://github.com/micro-ROS/micro_ros_platformio
# Install
* install Platform-IO extension for VS-Code
* install docker following https://docs.docker.com/engine/install/ubuntu/

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

verify that docker is running:
```
sudo systemctl status docker
```
test docker setup
```
 sudo docker run hello-world
```
add user to the docker group
```
sudo usermod -aG docker $USER
```
log in into the docker group (alternatively log out and log back in)
```
newgrp docker
```
# run
* first start docker container with micro-ROS agent:
```
docker run -it --rm -v /dev:/dev -v /dev/shm:/dev/shm --privileged --net=host microros/micro-ros-agent:$ROS_DISTRO udp4 --port 8888 -v6
```
* then run this example on Arduino
* Ethernet addresses/ports:
* IP arduino: 192.168.1.177
* IP agent: 192.168.1.113
* Port: 8888

* ros commands for testing:
```
ros2 topic pub /teensy_hub/subscribe_topic std_msgs/msg/Int32 "{data: 10}"
```
```
ros2 topic echo /teensy_hub/servo_pos_set
```

* center position of servos
```
ros2 topic pub /teensy_hub/servo_pos soro_msgs/msg/ServoCommands "{servo_micros: [1500, 1500, 1500, 1500, 1500, 1500, 1500, 15000, 1500, 1500, 1500, 1500]}"
```

* some othher ervo valuues. Not intended for use with robot.
```
ros2 topic pub /teensy_hub/servo_pos soro_msgs/msg/ServoCommands "{servo_micros: [15100, 15200, 15300, 15400, 15500, 15600, 15700, 15800, 15900, 15100, 15110, 15120]}"
```

* simple circular-like trajectory
```
ros2 run traj_publisher_example servo_talker
```

### troubleshooting:
## Fix ERROR:colcon.colcon_cmake.task.cmake.build:Could not find a shell extension for the command environment Failed <<< ament_cmake_python [0.02s, exited with code 1]
* clean everything:
```
pio run --target clean
rm -rf .pio/libdeps/
rm -rf ~/.micro_ros_dev
```

* update extensions
```
sudo apt update
sudo apt install python3-colcon-common-extensions python3-pip
pip3 install -U setuptools
```

* Force-rebuild micro-ROS dev deps
```
cd .pio/libdeps/teensy41_eth/micro_ros_platformio
rm -rf build/ install/ log/
pio run
```


## Install Docker engine
following [UR tutorial](https://docs.universal-robots.com/tutorials/ps5-urcap-tutorials/dev-env-setup/docker-setup.html),
[UR docs](https://docs.universal-robots.com/Universal_Robots_ROS2_Documentation/doc/ur_robot_driver/ur_robot_driver/doc/usage/simulation.html#usage-with-official-ur-simulator),
[UR docs](https://docs.universal-robots.com/Universal_Robots_ROS2_Documentation/doc/ur_client_library/doc/setup/ursim_docker.html)
### uninstall old versions of Docker engine
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```
### set up the Docker repository
* update apt package index
```
sudo apt-get update
```
* install packages to allow use of repositories over https
```
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release
```
* add Dockers GPG key
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
* set up the stable repository
```
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### install Docker engine
* update apt packege index again
```
sudo apt-get update
```
* install the latest version of Docker Engine, Docker CLI, and containerd
```
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

### start and enable Docker
* start Docker
```
sudo systemctl start docker
```
* autostart Docker at boot
```
sudo systemctl enable docker
```

### verify installation
* check Docker version
```
sudo docker --version
```
* run a test container
```
sudo docker run hello-world
```

### manage Docker as non-root user
* create the docker group if it does not exist (it most likely does)
```
sudo groupadd docker
```
* add current user to the group
```
sudo usermod -aG docker $USER
```
* log out and log in for group membership to take effect 

## Setup URSim
following [UR docs](https://docs.universal-robots.com/Universal_Robots_ROS2_Documentation/doc/ur_client_library/doc/setup/ursim_docker.html), more details: [UR Docker docs](https://hub.docker.com/r/universalrobots/ursim_e-series)
* start 
```
docker run --rm -it -e ROBOT_MODEL=UR10 -p 5900:5900 -p 6080:6080 --name ursim universalrobots/ursim_e-series
```

```
ros2 run ur_client_library start_ursim.sh -m ur10e
```
In case the container is already running stop it first:
```
docker stop /ursim
```
```
ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur10e robot_ip:=192.168.56.101
```

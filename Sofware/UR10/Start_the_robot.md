# Start the Driver
* Start the teach-pedant
* On the teach-pedant: Power on the robot -> start the robot -> press Exit
* On the PC: Start the driver with the physical robot:
```
ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur10e robot_ip:=192.168.56.101 launch_rviz:=true
```
> [!NOTE]
> This driver always has to run on the PC connected directly with the robot.

* On the teach-pedant: Go to Program -> Open -> Program -> select "external_control.urp" -> press Open
* On the teach-pedant: Switch to remote control mode
> [!NOTE]
> Control of the robot via the teach-pedant is only possible in local control mode. If e.g. the "external_control.urp" has to be restarted first switch back to local control mode.


* start the scaled joint trajectory tracking control example
```
ros2 launch ur_robot_driver test_scaled_joint_trajectory_controller.launch.py
```



# controllers:
https://github.com/ros-controls/ros2_controllers/blob/master/ros2_controllers_test_nodes/ros2_controllers_test_nodes/publisher_joint_trajectory_controller.py

https://github.com/UniversalRobots/Universal_Robots_ROS2_Driver/blob/main/ur_robot_driver/launch/test_scaled_joint_trajectory_controller.launch.py

* Start the driver with URSim (following [UR docs](https://docs.universal-robots.com/Universal_Robots_ROS2_Documentation/doc/ur_robot_driver/ur_robot_driver/doc/usage/simulation.html#usage-with-official-ur-simulator))
first start URSim
```
ros2 run ur_client_library start_ursim.sh -m ur10e
```
the GUI of the simulator can then be accessed by opening [http://192.168.56.101:6080/vnc.html](http://192.168.56.101:6080/vnc.html) in a browser.
then start the driver:
```
ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur10e robot_ip:=192.168.56.101 launch_rviz:=true
```

# other
```
ros2 run plotjuggler plotjuggler
```

# Motion capture
* start VRPN stream on motion capture PC
* start ROS2 VRPN mocap bridge
```
ros2 launch vrpn_mocap client.launch.yaml server:=134.28.27.106 port:=3883
```



# some test
```
ros2 run ur_client_library start_ursim.sh -m ur10e
```
* connect to VNC 
* power on the robot (in VNC, to normal mode)
* move robot to 0°; -90°; 0°; -90°; 0°, 0°
* Run->load program->external program->external control
*start program
```
ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur10e robot_ip:=192.168.56.101 launch_rviz:=true
```
```
ros2 launch ur_robot_driver test_scaled_joint_trajectory_controller.launch.py
```
*make sure program is still running

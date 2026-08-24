
# 🤖 Intro / Current State
The ros-hub repo is used for ros interfacing with the UR10e rigid robot and the soft robot (soro).

The repo has the required libraries to run the real-time control, assuming the other steps for universal robot setup in the SoftRobotics-MuM wiki have already been completed. It also contains the required packages to run the qualysis bridge to communicate with the motion capture data over ROS2. This is from HippoCampusRobotics (https://github.com/HippoCampusRobotics/qualisys_bridge) and any required dependencies for it should be included in the ros-hub repo.

#### Rigid Robot Current Functionality - rigid_control package
The current functionality involves real-time control with MoveIt! servo, where one ROS2 node publishes a real-time trajectory (cartesian coordinates) and another ROS2 node subscribes to it and applies a velocity with a P-controller to move the UR10e end-effector to this position. As of writing this, it does a simple circular path motion, but the code may be modified to just move to a certain position to test the offset control with Qualysis coordinate frame which has been set up. This is launched through **live_control.launch.py** in the `system_bringup` package. This is not very useful anymore as we have the combined system, unless you need reference to how MoveIt! servo jogging works for real-time.

#### Soft Robot Current Functionality - soft_control package
The current functionality involves running a PyTorch model to adjust the three servos (yellow soro) to an x & y coordinate position. The qualysis body is called 'bens_soro' when running the qualysis bridge. This is not very useful anymore as we have the combined system.

#### Combined System Current Functionality - combined_control package
This mixes the rigid robot and the soft robot attached to it. The rigid robot runs MoveIt! servo to jog the position and orientation of the TCP in real time, while using a neural network to predict the length of the soft robot at a straight position at its current orientation. This distance is maintained from the goal point while aligning its z-axis. The soft robot, attempting to keep its position straight, also adjusts as the orientation of the TCP changes, using another neural network to change its servo values based on the relative gravity vector.

***

# 🛠️ Clone Repo/Initial Setup
Create a workspace folder (e.g. 'ros2_ws') and clone the ros-hub repo:  
```bash
git clone https://github.com/SoftRobotics-MuM/ros-hub.git .
```

Install for managing/debugging controllers for rigid robot:  
```bash
sudo apt install ros-jazzy-ros2controlcli
```

***
# ✅ Running the Rigid Robot System
After building your workspace with the ros-hub resources and sourcing as needed, follow these steps:  

#### ➥ Run on physical robot:
1. Turn on teach pendant, wait for startup, turn on the robot and release brakes  
2. Run UR driver: 
    ```bash
    ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur10e robot_ip:=192.168.57.101 launch_rviz:=true  
    ```

#### ➥ Run on URSim
1. Run URSim: 
    ```bash
    ros2 run ur_client_library start_ursim.sh -m ur10e
    ```
2. Run UR driver: 
    ```bash
    ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur10e robot_ip:=192.168.56.101 launch_rviz:=true
    ```

## Launch the System
_Ensure the robot is in remote operation mode._
In your ROS2 workspace, run the following: 
```bash
ros2 launch system_bringup live_control.launch.py
```

After some loading, the robot should be to a position similar to the home position, and eventually start moving.
In order for the qualysis connection to work with this launch file, ensure that you defined a rigid body in QTM and follow the instructions in the readme on the qualysis bridge repo: https://github.com/HippoCampusRobotics/qualisys_bridge


# 🔍 How the Rigid Robot Launch File Works
The launch file follows a well-defined sequence to move the robot, set up the required controllers, and switch to real-time control using MoveIt! Servo. The steps are as follows:

1. **Move the Robot to Home Position:**  
   The launch file begins by moving the robot to a predefined home position using a ROS2 node, **move_to_home.py**.

2. **Run the Qualisys Bridge:**  
   The Qualisys Bridge launch file is launched to facilitate motion capture data communication with ROS2. Be sure to configure the `qualysis_bridge/config/bridge.yaml` according to the instructions on https://github.com/HippoCampusRobotics/qualisys_bridge.

3. **Launch MoveIt! Servo:**  
   After the robot is at the home position, the launch file proceeds to launch MoveIt! Servo, which enables real-time control of the robot for Cartesian movement.

4. **Switch Controllers:**  
   The controller is switched from the `scaled_joint_trajectory_controller` to the `forward_position_controller`. This change is necessary for MoveIt! Servo to control the robot joints' velocity in real time.
   
   >  Note: The _`scaled_joint_trajectory_controller`_ is used initially to move the robot to the home position via the `/scaled_joint_trajectory_controller/joint_trajectory` topic.

5. **Switch Command Type:**  
   Once the controller switch is successful, the launch file updates the Servo command type to 1. This action tells the MoveIt! Servo node to interpret incoming control messages as TWIST commands (Cartesian jogging) instead of joint positioning commands.

6.  **Launch Controller and Publisher Nodes:**  
Finally, with the required setup complete, the following controller nodes are launched to handle real-time motion control:
* `real_time_control.py`
* `real_time_publisher.py`

***
# ✅ Running the Soft Robot System
After building your workspace with the ros-hub resources and sourcing as needed, follow these steps:  

#### ➥ Run Soft Robot (soro) Connection:
1. Run docker connection
    ```bash
    docker run -it --rm -v /dev:/dev -v /dev/shm:/dev/shm --privileged --net=host microros/micro-ros-agent:$ROS_DISTRO udp4 --port 8888 -v6
    ```
2. Plug in the soro ethernet and power cable. The console window with the command from _step 1_ should be continuously printing now.
3. Source python virtual environment (required to run PyTorch inference in ROS2 environment):
    3.1 If no python virtual environment has been set up for your local repo, first create the venv in your ros2_ws folder:
    ```bash
    python3 -m venv .venv
    source .venv/bin/activate
    pip install -r requirements.txt
    ```
    3.2 If you already have the venv with the .venv folder in your workspace, simply run:
    ```bash
    source .venv/bin/activate
    ```
    
4. Run the script you need (this may later be updated to final functionality when completed). For example, run `ros2 run soft_control run_model.py`. This uses old models trained for the soft robot that are in the soft_control package that are not useful for the combined system anymore.
***
# ✅ Running the Combined Robot System
After building your workspace with the ros-hub resources and sourcing as needed, follow these steps.

1. Run docker connection
    ```bash
    docker run -it --rm -v /dev:/dev -v /dev/shm:/dev/shm --privileged --net=host microros/micro-ros-agent:$ROS_DISTRO udp4 --port 8888 -v6
    ```
2. Plug in the soro ethernet and power cable. The console window with the command from _step 1_ should be continuously printing now.
3. Source python virtual environment (required to run PyTorch inference in ROS2 environment):
    3.1 If no python virtual environment has been set up for your local repo, first create the venv in your ros2_ws folder:
    ```bash
    python3 -m venv .venv
    source .venv/bin/activate
    pip install -r requirements.txt
    ```
    3.2 If you already have the venv with the .venv folder in your workspace, simply run:
    ```bash
    source .venv/bin/activate
    ```

4. Turn on teach pendant, wait for startup, turn on the robot and release brakes  
5. Run UR driver: 
    ```bash
    ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur10e robot_ip:=192.168.57.101 launch_rviz:=true  
 6. Run the launch file: 
    ```bash
    ros2 launch system_bringup combined_control.launch.py 
    ```
The launch file works similar to the rigid_control launch file with less setup and movement to the home position. It needs some time upon running because it needs to set up the models, etc.


# 🧬 Model Details (for Combined Control)
There are two models used for the combined system. 

The first model returns the required servo values for a desired relative X and Y coordinates. It takes in five inputs: (X, Y) as the goal position, and the x, y, z components of the relative gravity vector (can be obtained from the TCP of the rigid robot the soft robot is mounted on). The model outputs a tensor of 3 values for the servo PWM signals. Index 0, 2, and 4 in the in the ROS2 servo_msgs vector (used to control the servo motors) are populated by the output of the model. The other 9 indices of the servo message can be set to 0 or any other value as they are not used. Some of the old models that can be found in the workspace have an additional three inputs- the previous servo values. This is used only in previous iterations of the project to deal with special cases involving z positioning but is no longer needed and this makes the system more jittery and less accurate.

The second model predicts the z value (the length) of the soft robot from the TCP of the rigid robot. The inputs are the three current servo values and the relative gravity vector as well. 

Using these two models the system is able to work. The rigid robot moves its TCP toward the goal position, while making its TCP align its z-axis with the goal point. As it changes orientation, the soft robot adjusts to the gravity to maintain its x-y position at (0, 0) relative to the TCP using the first model. At the same time, the second model is running to predict the distance from the TCP to the tip of the soft robot, and maintains this distance from the goal point at the TCP using the second model.

The models can be found in the combined_control package in the models folder. There are many models there, and the servo_prediction_x models (1-5) all include the previous servo value models. servo_prediction_no_prev is the currently used model that is better for the system. The z_prediction model contains the second model (for length prediction) and is also currently used.

The training_data folder contains data used to train the system. The 'soro' subfolder is not useful anymore. The 'combined' folder contains a lot of 'augmented' data which has data with previous servo values (we do not use this anymore). The regular 'combined_data.csv' file contains the data that the combined system is trained on, using the 'data_collection.py' script in the combined_control package.

The actual training notebooks can be found in the 'model_training_scripts' folder in the src directory. One file is used to train the servo value prediction model and the other is used to train the z prediction. Both can use the same dataset in training.
***

# 📹 ros2bag_recordings (folder in ros2_ws)
This stores ros bag recordings. Any ros bag recordings should be ran from ros2_ws/ros2bag_recordings to maintain structure and organization. The 'tilted_circle' text file explains what the titled circle folders are recordings of. The simulation folders are recordings ran from ur_sim rather than the physical robot for testing purposes and are not very useful anymore. They were just used to check the smoothness of the system without running the physical system. The topics recorded for the physical system are as follows:
- /real_time_combined_control/goal_position
- /bens_soro/odometry_naive
- /teensy_hub/servo_pos
- /servo_node/delta_twist_cmds
The ros2 bag recordings with 'final' in its name are the final recordings that may be useful for analysis or something.




***
# 📂 ros-hub Packages Structure
## 🔹hippo_common & px4_msgs
Dependencies required for qualysis_bridge, do not need to be modified or accessed.

## 🔹qualysis_bridge
Package used for communication with Qualysis Track Manager running on Windows machine.
To run qualysis_bridge, ensure that Qualysis Track Manager software is running on the other (Windows) machine. Define a body in the software, and modify the **config/bridge.yaml** file as required (see the qualysis_bridge repo for specific details). Then, launch: 
```bash
ros2 launch qualisys_bridge qualisys_bridge.launch.py
```
After launching, you should see multiple topics listed for `ros2 topic list` with the name of your defined rigid body in Qualysis.


## 🔹system_bringup 
Contains primary launch files required to run rigid robot (and eventuall soro as well!).
#### ➥ Rigid Robot (not used anymore)
###### 🔸`live_control.launch.py`
The primary launch file to run rigid robot real time control trajectory
###### 🔸`scripts/check_moveit_servo_running.py`
A script used by the launch file to ensure MoveIt! Servo has completed setup and real-time control procedure can proceed.
#### ➥ Combined Robot System
###### 🔸`combined_control.launch.py`
The primary launch file to run the combined system (currently runs the tilted circle trajectory). Takes some time to set up, once it is finished it runs. Similar the live_control.launch.py but doesn't reset controller type or return to home position beforehand.

## 🔹rigid_control
Package with code that controls the UR10e rigid robot.
###### 🔸`scripts/real_time_control.py` & `scripts/real_time_publisher.py`
The ROS2 nodes required to run real-time cartesian motion as launched by the launched file.
The control node uses MoveIt! servo to move towards the published cartesian coordinate from the publisher node. The publisher node also applies a TF to the desired coordinates. The desired coordinates should be from the qualysis frame, and the TF in the publisher will apply changes to that to move the robot accurately to the qualysis coordinates. Must ensure that the axes defined on the robot and qualysis align for this (+ve x-axis faces towards the wave generator and z-axis faces upwards)
###### 🔸`scripts/move_to_home.py` & `scripts/move_to_lab_safe.py`
Two scripts that use `scaled_joint_trajectory_controller` to move robot to specified joint configuration. `move_to_home.py` is similar to the actual home position of the robot, but slightly bend at the elbow to ensure trajectory planning is more consistent and goes towards the camera system. `move_to_lab_safe.py` is also a pre-configured pose that brings the end effector to a position in the camera workspace.
###### 🔸`scripts/rtde_move_to_lab_safe.py` & `scripts/rtde_triangle_rounded.py`
Scripts that use RTDE control to move the robot. `rtde_triangle_rounded.py` is a simple triangular movement demo with rounded corners. The motion is very consistent and smooth, with the ability to plan cartesian trajectories with rounded corners, however it is **not ROS2 compatible**. Running these scripts and then trying to run ROS2-based robot controls will result in the robot not moving (must restart the UR driver).
###### 🔸`scripts/test_cartesian_velocity_control.py`
An old node used to test and verify MoveIt! Servo functionality.
###### 🔸`scripts/test_motion.py`
The first script in this repo, an old node runs three simple joint configurations to test movement and general robot connection.
###### 🔸`src/moveit_trajectory_demo.cpp`
An old node used for experimenting spline paths in cartesian coordinates using interpolation.

## 🔹soft_control
Package with code that controls the soro and runs the model.
###### 🔸`scripts/example_control.py`
A simple demo to move the soro in a circular trajectory
###### 🔸`scripts/move_soro.py`
Automated data collection script. Moves the soro in circles of varying sizes, with 10 points in each circle. Saves the coordinates as seen in qualysis to a csv AFTER FULL TRAJECTORY IS COMPLETE. Ensure robot is in the fully upright (neutral) position and the end effector is a defined rigid body in QTM working with qualybefore running this script
###### 🔸`scripts/run_model.py`
Runs the model to move the robot to a desired (X, Y) position. Must be run with venv active.

## 🔹combined_control
Package with code that controls the combined system.
###### 🔸`models folder`
Contains many models that have been trained and tested. The main models that are used (since we no longer use previous servos for predictions) is servo_prediction_no_prev and z_prediction.
###### 🔸`scripts/sample_trajectorycombined.py`
Not useful script as we use separate pub and sub nodes to move to specified trajectories in real time now.
###### 🔸`scripts/data_collection.py`
The data collection script to collect data for the combined system. Must be ran with qualysis. Each run does 10 twists at a specified TCP orientation along a circle plane. The orientation should be changed after each run (circle split into 12 sections, just change the multiplication on line 37 to something between 0 to 12, although 0 and 6 are just straight up or straight down and don't need all 10 twists ran). Upon ending the run the csv file will append the data (servo values, gravity vectors, quaternion TCP orientation, etc).
###### 🔸`scripts/combined_circle_trajectory_publisher.py, scripts/combined_triangle_trajectory_publisher.py, scripts/combined_square_trajectory_publisher.py, scripts/combined_infinity_trajectory_publisher.py```
Publish a trajectory for the combined system to follow (a tilted shape, either circle, triangle, square or a set of curves). This is one of the two components that MUST RUN for the combined system to move. It is ran in the launch file that in 'system_bringup' package that runs the combined system. Should be ran AFTER the subscriber node (explained in the next point) is started, which is also started by the launch file.
###### 🔸`scripts/combined_trajectory_follower.py`
The subscriber that runs the models and MoveIt! servo control of the combined system. MUST RUN for the combined system to move and also gets ran by the launch file. Below is a point form explanation on the steps that can be taken to run the combined system with the titled circle trajectory without using the launch file:

- Source the virtual environment in ros2_ws: `source .venv/bin/activate`
- Start the ur_driver (after this you may start the 'external control' program on the teach pendant): `ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur10e robot_ip:=192.168.57.101 launch_rviz:=true `
- Start moveit: `ros2 launch ur_moveit_config ur_moveit.launch.py ur_type:=ur10e launch_servo:=true launch_rviz:=false`
- Switch controller type: `ros2 control switch_controllers --deactivate scaled_joint_trajectory_controller --activate forward_position_controller`
- Switch servo command type: `ros2 service call /servo_node/switch_command_type moveit_msgs/srv/ServoCommandType "{command_type: 1}"`
- Start the combined trajectory follower: `ros2 run combined_control combined_trajectory_follower.py`
- Start the circle trajectory publisher: `ros2 run combined_control combined_circle_trajectory_publisher.py`

## 🔹useful_scripts
Package with code that has many useful python scripts (can be just ran with Python3) for data modification and plotting. Be sure to source the ros2_ws virtual environment before running any of the scripts as it has the required libraries.
###### 🔸`/combined_validation_plotter`
This is used to plot the validation results after training a model using a notebook (the validation results CSV is created during the training process)
###### 🔸`/data_cleanup`
This is used to clean the csv data file provided by removing duplicate lines. This was useful when collecting data for the models with qualysis since the cameras could not always capture every orientation with their positions.
###### 🔸`/prev_servo_dataset_creator`
This is used to augment training data by adding previous servo values (a custom amount and range) to each line of data for the combined system and creates a new csv file
###### 🔸`/ros2_bag_plotter`
This contains codes that plot rosbag files. It uses only the .mcap files (from the rosbag recordings in the ros2bag_recordings folder in ros2_ws).
###### 🔸`/soro_validation_plotter`
Used to plot validation results after training the soft robot models (not for combined system). No longer useful.


***
# 💡Useful Notes and Commands

### Useful websites for ROS2 development and UR
* UR Controllers Documentation
> ➥  https://docs.universal-robots.com/Universal_Robots_ROS_Documentation/doc/ur_robot_driver/ur_robot_driver/doc/usage/controllers.html

* MoveIt Servo Documentation for General Cartesian and real-time control
> ➥  https://moveit.picknik.ai/main/doc/examples/move_group_interface/move_group_interface_tutorial.html  
> ➥  https://moveit.picknik.ai/main/doc/examples/realtime_servo/realtime_servo_tutorial.html  
> ➥  https://index.ros.org/p/moveit_visual_tools/  

### List of useful commands
* `ros2 control list_controllers`
> This command lets you see what controllers are active/inactive, so you can use the right ros topic to communicate to move ur10e or see which ones are ACTIVE. Inactive controller topics will ignore any motion commands.

* `ros2 control switch_controllers --deactivate <controller_to_deactivate> --activate <controller_to_activate>`
> Activates one controller and deactivates another. You can see which controllers are active/inactive with the `list_controllers` commands above. This command was not very reliable in the launch file for some unknown reason, thus `live_control.launch.py` activates and deactivates controllers in separate commands instead of both together.


***

# 🚩 Past Issues worth exploring
**1. MoveIt! Servo ignoring published movement commands**  

When launching MoveIt! Servo and running commands with nodes, it was noticed that the robot would not always respond to commands and would remain stationary despite correct setup procedure being followed (when running on simulation, not physical robot). Issue was determined to be that MoveIt! was waiting for an update on robot state before enabling Servo, posting the following message:
> Waiting to receive robot state update.  

A simple fix is to manually move the robot prior to switching it to remote (but after MoveIt! is started), allowing MoveIt! to get a robot state update and enable Servo, so the above message stops printing. This works well but is tedious, so it is worth exploring the following resource to see if a quicker and robust alternative is possible: https://github.com/moveit/moveit2/issues/3040.

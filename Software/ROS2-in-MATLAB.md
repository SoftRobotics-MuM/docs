# ROS2 in MATLAB

## Required software 

### MATLAB
- version MATLAB R2025b
- ROS Toolbox
  
### Python
- Python 3.10
Python is required by the ROS Toolbox to generate support for custom ROS 2 messages.

### C++ Compiler

- **Ubuntu:** GCC / G++
- **Windows:** Visual Studio 2019 or 2022

The compiler is required by the ROS Toolbox to generate support for custom ROS 2 messages.

## Configure Python in MATLAB

Find the path to the Python 3.10 executable.

**Windows:**
```bash
py -3.10 -c "import sys; print(sys.executable)"
```
**Ubuntu:**
```bash
python3.10 -c "import sys; print(sys.executable)"
```

Then open the **ROS Toolbox Preferences** in MATLAB and set the Python executable to this path.

Recreate the Python environment.


## Generate the Custom ROS 2 Messages

MATLAB must generate support for the custom `soro_msgs` message types before they can be used.

Download the [`soro_msgs`](https://github.com/SoftRobotics-MuM/teensy-hub/tree/main/teensy_ethernet_hub_v2/extra_packages/soro_msgs) package.

Place the complete `soro_msgs` package inside a parent directory, for example:

```text
custom/
└── soro_msgs/
    ├── package.xml
    └── msg/
        └── ServoCommands.msg
```

In MATLAB, run `ros2genmsg` with the path to the parent directory:

```matlab
ros2genmsg("/path/to/custom")
```


## Verify the Custom Messages

In MATLAB, run:

```matlab
ros2 msg list
```

The list should contain:

```text
soro_msgs/ServoCommands
```


## Basic Matlab code for ROS2 interaction
```
%% ROS 2 Subscriber for Teensy Hub
clear;
% Explicitly set the Domain ID
setenv("ROS_DOMAIN_ID", "72");

%% publisher
node_pub = ros2node("/teensy_hub/servo_pos");
servo_cmd_pub = ros2publisher(node_pub,"/teensy_hub/servo_pos","soro_msgs/ServoCommands");

%% subscriber
node_sub = ros2node("/matlab_servo_cmd_sub");
servo_cmd_sub = ros2subscriber(node_sub,"/teensy_hub/servo_pos",@servo_cmd_sub_callback);

%% publish
while true 
    servo_cmd_msg = ros2message(servo_cmd_pub);
    servo_cmd_msg.servo_micros = uint16(ones(12,1)*1500);
    send(servo_cmd_pub,servo_cmd_msg);

    pause(0.1);
end

function servo_cmd_sub_callback(message)
    % Process the received message (example: display the servo positions)
    disp("Servo micros: ");
    disp((message.servo_micros).');
end
```

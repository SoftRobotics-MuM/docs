# Software Overview
## System Overview
The basic structure of the software system is visualized here.

```mermaid
flowchart LR

    Custom["Custom Code"]

    subgraph Ubuntu["Ubuntu Lab PC"]
        MicroROS["micro-ROS Bridge"]
        QualisysBridge["Qualisys Bridge"]
    end

    Teensy["Teensy"]

    subgraph Robot["Soft Robot"]
        Servos["Servos"]
        SensorBoard["Sensorplatine<br/>• IMU<br/>• Magnetometer<br/>• LED<br/>• MCU"]
    end

    subgraph Windows["Windows Lab PC"]
        Qualisys["Qualisys"]
    end

    Custom <-->|ROS| Ubuntu
    MicroROS <-->|micro-ROS| Teensy


    Teensy -->|PWM| Servos
    Teensy <-->|"SPI" Sensorplatine| SensorBoard

    Qualisys --> QualisysBridge
    Qualisys -.-> |visual tracking| Robot

classDef Blau fill:#e3f2fd,stroke:#1565c0,color:#000;
classDef Gelb fill:#fff3e0,stroke:#ef6c00,color:#000;
classDef Rot fill:#fce4ec,stroke:#c62828,color:#000;

class Robot Rot;
class Custom,Windows,Ubuntu Gelb;
```
## Work With The System
### 🦄 Initial Setup
For the micro-ROS bridge
- You need to [install micro-ROS](www.todo.de) on your Ubuntu computer.

For the custom code
- If you work with **Matlab**, use the [ROS2 in Matlab](www.todo.de) documentatin.
- If you work with **python** or **C++**, install [ROS2 Jazzy](https://docs.ros.org/en/jazzy/Installation.html).

- [trouble shooting](www.todo.de)

For the Qualisys bridge
- This is only needed if you want to work with the camera system.
- Follow the HippoCampus [Qualisys Documentation](https://github.com/HippoCampusRobotics/qualisys_bridge)


### 🐡 Flash The Robot
The robot doesn't need to be flashed every time you work with it. By default the robots are flashed with [this](www.todo.de) code. To change that, follow the [not existing documentation](www.todo.de)

### 🐊 Before Each Session

### 🐕 Do Stuff
Flash the robot

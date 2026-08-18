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
## Documentation
### General documentation:
Contains instructions and documentations that are needed to work with the system.
Soft robots
- [micro-ROS Bridge](www.todo.de)
- [Qualisys Bridge](www.todo.de)

Rigid robot
- [dies](www.todo.de)
- [und](www.todo.de)
- [das](www.todo.de)

### Project specific documentations:
Contain documentations for specific projects and are not needed to work with the system.
- [latency Measurement](www.todo.de)

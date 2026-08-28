# Robots
There are various tendon-actuated robots in use. For every robot there is a directory in the [cloud](https://cloud.tuhh.de/index.php/apps/files/files/223638968?dir=/SoftRobotics-MuM/CAD_Source), containing the corresponding CAD files and documentation.

The `00_Documentation` directory of each robot contains relevant documentation, such as the **bill of materials** and, where available, **manufacturing instructions**.

For information about the **CAD directory** structure and file naming conventions, see [CAD file structure](file-structure.md).

For information about how to **manufacture the silicone bodies**, see the [silicone instructions](silicone-casting.md) and the individual Mold instructions

## Existing Tendon-Actuated Robots

- [`RT3`](#rt3) - Tapered 3-Tendon Robot
- [`RT6`](#rt6) - Tapered 6-Tendon Robot
- [`RR`](#rr) - Retractable Robot
- [`RL`](#rl) - Introduction to Robotics Robot
- [`GR`](#gr) - Gripper
- [`RS`](#rs) - Straight 6 Element Robot


<a name="rt3"></a>
## `RT3` – Tapered 3-Tendon Robot

<img src="Images/robot_rt3.jpg" alt="RT3 Robot" width="200" align="right">

This robot is the standard design of the MuM soft robots. The RT3 version consists of one element, three tendons.

[CAD-Files](https://cloud.tuhh.de/index.php/apps/files/files/223639088?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RT3_Robot_tapered-3strings)

[Bill of Materials](https://cloud.tuhh.de/index.php/apps/files/files/223639331?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RT3_Robot_tapered-3strings/00_Documentation)

There are no individual mold instructions until now. But it's basically the same as the 6-Tendon robot with less parts.

[Manufacturing instructions RT6](https://cloud.tuhh.de/index.php/apps/files/files/223639109?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RT6_Robot_tapered-6strings/00_Documentation)

<br clear="right">


<a name="rt6"></a>
## `RT6` – Tapered 6-Tendon Robot

<img src="Images/robot_rt6.jpg" alt="RT6 Robot" width="200" align="right">

This robot is the standard design of the MuM soft robots. The RT6 version consists of two elements, each actuated by three tendons. 

[CAD-Files](https://cloud.tuhh.de/index.php/apps/files/files/223639109?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RT6_Robot_tapered-6strings)

[Bill of Materials](https://cloud.tuhh.de/index.php/apps/files/files/223639109?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RT6_Robot_tapered-6strings/00_Documentation)

[Mold instructions](https://cloud.tuhh.de/index.php/apps/files/files/223639109?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RT6_Robot_tapered-6strings/00_Documentation)

<br clear="right">

<a name="rr"></a>
## `RR` – Retractable Robot

<img src="Images/robot_rr.jpg" alt="RR Robot" width="200" align="right">

The retractable robot is designed to change its length, allowing it to reach a larger workspace. It can be built with either three or six tendons.

Known **issues**:
- The mold consists of many individual parts, making the casting process complex.
- The tubes used to reduce friction tend to slip out of their holes.

[CAD-Files](https://cloud.tuhh.de/index.php/apps/files/files/223639046?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RR_Robot_retractable)

[Bill of Materials](https://cloud.tuhh.de/index.php/apps/files/files/223639049?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RR_Robot_retractable/00_Documentation)

[Manufacturing instructions](https://cloud.tuhh.de/index.php/apps/files/files/223639049?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RR_Robot_retractable/00_Documentation)

<br clear="right">


<a name="rl"></a>
## `RL` – Introduction to Robotics Robot

<img src="Images/robot_rl.jpg" alt="RL Robot" width="200" align="right">

This is the robot that is used in the course '**Introduction to robotics**'. The purpose of this robot is to be controlled with visual localization of the April-Tags

The silicone body of this robot is the [`RT3`](#rt3) - Tapered 3-Tendon Robot.

The Robot is driven by three tendons. For this robot we have a special base plate with less servo spaces and an engraved coordinate system to place an April-Tag. The CAD-files also contain the parts of the frame.

[CAD-Files](https://cloud.tuhh.de/index.php/apps/files/files/223639028?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RL_Robot_introductions-to-robotics)

[Bill of Materials](https://cloud.tuhh.de/index.php/apps/files/files/223639031?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RL_Robot_introductions-to-robotics/00_Documentation)

[corresponding software documentation](https://github.com/SoftRobotics-MuM/course-introduction-to-robotics)

<br clear="right">




<a name="gr"></a>
## `GR` – Gripper

<img src="Images/gripper_gr.jpg" alt="Gripper" width="200" align="right">

[CAD-Files](https://cloud.tuhh.de/index.php/apps/files/files/223638971?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/GR_Gripper)

<br clear="right">



<a name="rs"></a>
## `RS` – Straight 6 Element Robot 

<img src="Images/robot_rs.jpg" alt="RS Robot" width="200" align="right">

This is an earlier version of the standard soft robot and is replaced by the `RT6` – Tapered 6-Tendon Robot.

[CAD-Files](https://cloud.tuhh.de/index.php/apps/files/files/223639070?dir=/SoftRobotics-MuM/CAD_Source/20_Robots/RS_robot_straight_6)

<br clear="right">

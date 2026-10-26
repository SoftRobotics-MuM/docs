# PC Setup: Lab PC and Development PC

This page describes how to set up the two kinds of computers used with the soft robot:

| role | runs | workspace |
|---|---|---|
| **Lab PC** (Ubuntu, in the lab) | micro-ROS agent (Teensy hub), Qualisys bridge, sensor bridge, sensor calibration, hardware tests | `~/soro/hardware_ws` from `hardware.repos` + `~/soro/agent_ws` |
| **Development PC** (any Ubuntu PC in the lab network) | controllers, trajectory generation, analysis | `~/soro/control_ws` from `control.repos` |

A development PC never talks to the hardware directly: it subscribes to and publishes standard ROS 2 topics, the lab PC does the rest. Everything that talks to the hardware runs on the lab PC, so that the Teensy timestamps (micro-ROS agent clock) and the mocap timestamps (Qualisys bridge) come from the same clock.

```
 Development PC(s)              Lab PC                               Robot
+------------------+  ROS 2   +---------------------------+ micro-ROS +------------+
| controllers      |<-------->| micro-ROS agent           |<--------->| Teensy hub |--> servos
| (control_ws)     | domain 72| soro_sensor_bridge        | UDP 8888  |            |<-- sensors
+------------------+          | qualisys_bridge           |           +------------+
                              | soro_calibration          |<-- Qualisys (Windows PC)
                              | (hardware_ws, agent_ws)   |
                              +---------------------------+
```

## Repositories

All repositories are in the [SoftRobotics-MuM](https://github.com/SoftRobotics-MuM) organisation (except the pinned upstream ones):

| repository | content | used on |
|---|---|---|
| [soro_interfaces](https://github.com/SoftRobotics-MuM/soro_interfaces) | message definitions (`soro_msgs`) | all PCs and the Teensy firmware (git submodule) |
| [soro_hardware_io](https://github.com/SoftRobotics-MuM/soro_hardware_io) | `soro_sensor_bridge`, `soro_calibration`, `soro_delay_test` | lab PC |
| [soro_bringup](https://github.com/SoftRobotics-MuM/soro_bringup) | launch files, robot configurations (`robots/<robot>/`), `hardware.repos`, `control.repos` | all PCs |
| [teensy-hub](https://github.com/SoftRobotics-MuM/teensy-hub) | Teensy firmware (PlatformIO) | PC used for flashing |
| qualisys_bridge, hippo_common ([HippoCampusRobotics](https://github.com/HippoCampusRobotics)), px4_msgs ([PX4](https://github.com/PX4/px4_msgs)) | mocap bridge and its dependencies, pinned to tested commits in `hardware.repos` | lab PC |
| soro_control *(planned)* | controllers (`soro_mpc`, `soro_trajectory`) | development PCs |

The workspaces are assembled from the `.repos` files in `soro_bringup` with [vcstool](https://github.com/dirk-thomas/vcstool), the standard tool for multi-repository ROS 2 workspaces. The `.repos` files pin the versions that work together; `soro_interfaces` must have the same version on all PCs and in the firmware.

---

## 1. Common base setup (both PC types)

### 1.1 Ubuntu and ROS 2

- Ubuntu 24.04
- ROS 2 Jazzy: follow the [official installation](https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html) (`ros-jazzy-desktop` and `ros-dev-tools`).

### 1.2 Tools

```zsh
sudo apt install git python3-vcstool python3-colcon-common-extensions python3-rosdep
sudo rosdep init        # only once per PC; "already initialized" is fine
rosdep update
```

### 1.3 GitHub access

The `.repos` files use https URLs. To push, you need a GitHub account with access to SoftRobotics-MuM and a token instead of a password. The easiest way is the GitHub CLI:

```zsh
sudo apt install gh
gh auth login           # GitHub.com, HTTPS, login with a web browser
gh auth setup-git       # git uses the gh login for https
```

(Alternatively use SSH keys, then change the remotes to `git@github.com:...`.)

### 1.4 Shell environment

We use **zsh** as shell (`sudo apt install zsh && chsh -s $(which zsh)`, then log in again). All commands on this page use the zsh variants of the ROS setup files (`setup.zsh`, `local_setup.zsh`); in bash use the `.bash` files instead.

Add to `~/.zshrc`:

```zsh
export ROS_DOMAIN_ID=72
source /opt/ros/jazzy/setup.zsh
# the workspace of this PC (see below), e.g.
source ~/soro/hardware_ws/install/setup.zsh     # lab PC
# source ~/soro/control_ws/install/setup.zsh    # development PC
```

> [!IMPORTANT]
> Source only **one** workspace of this setup per terminal, and build a workspace in a terminal where **no other workspace** is sourced. colcon records every workspace sourced at build time as an underlay of the new one; two workspaces with the same packages then shadow each other in a hard-to-debug way.

### 1.5 Network and firewall

- All PCs must be in the same network (subnet) so that ROS 2 discovery (multicast) works, and use `ROS_DOMAIN_ID=72`.
- Open the ROS 2 ports if `ufw` is active, see [ROS setup trouble shooting](ROS-Setup_trouble-shooting.md).
- PCs that compare their timestamps with the lab PC's data (e.g. a controller that uses sensor stamps) should synchronise their clock to the lab PC, e.g. with `chrony`.

---

## 2. Lab PC

### 2.1 Workspace

```zsh
mkdir -p ~/soro/hardware_ws/src && cd ~/soro/hardware_ws
git clone https://github.com/SoftRobotics-MuM/soro_bringup.git src/soro_bringup
vcs import --skip-existing src < src/soro_bringup/hardware.repos
rosdep install --from-paths src -iy
colcon build --symlink-install           # px4_msgs takes a few minutes
source install/setup.zsh
```

`--symlink-install` links Python files, launch files and configurations from `src/` into `install/`: changes to them (e.g. a new `calibration.yaml`) take effect without rebuilding.

Check:
```zsh
colcon test --packages-select soro_calibration soro_sensor_bridge soro_delay_test
colcon test-result --verbose
```

### 2.2 micro-ROS agent

The agent connects the Teensy hub (micro-ROS over UDP) to ROS 2. Build it natively in its own workspace (it brings its own micro-ROS packages, so it must not be part of `hardware_ws`):

```zsh
mkdir -p ~/soro/agent_ws/src && cd ~/soro/agent_ws
git clone -b jazzy https://github.com/micro-ROS/micro_ros_setup.git src/micro_ros_setup
rosdep install --from-paths src --ignore-src -y
colcon build
source install/local_setup.zsh
ros2 run micro_ros_setup create_agent_ws.sh
ros2 run micro_ros_setup build_agent.sh
```

Start it (in its own terminal, with `~/soro/agent_ws/install/local_setup.zsh` sourced):
```zsh
ros2 run micro_ros_agent micro_ros_agent udp4 --port 8888 -v4
```

Alternative without building: the Docker image (see [micro-ROS](micro-ROS.md) for installing Docker):
```zsh
docker run -it --rm --net=host microros/micro-ros-agent:jazzy udp4 --port 8888 -v4
```

> [!NOTE]
> Use verbosity `-v4` (or lower). `-v6` prints every message and adds a few ms of delay per command.

### 2.3 Network to the Teensy hub

The firmware uses fixed addresses:

| | IP | port |
|---|---|---|
| Teensy hub | 192.168.1.177 | |
| micro-ROS agent (lab PC) | **192.168.1.113** | 8888/udp |

Give the lab PC's network interface to the Teensy the static address `192.168.1.113/24` (Ubuntu settings → Network → wired connection → IPv4 → manual) and allow UDP port 8888 in the firewall.

### 2.4 Teensy firmware (only if it has to be changed or re-flashed)

Normally the Teensy keeps its firmware. To build and flash:

1. Install VS Code with the PlatformIO extension. For the command line add PlatformIO to the `PATH`:
   ```zsh
   echo 'export PATH="$PATH:$HOME/.platformio/penv/bin"' >> ~/.zshrc
   ```
2. Clone **with** the `soro_interfaces` submodule:
   ```zsh
   git clone --recursive https://github.com/SoftRobotics-MuM/teensy-hub.git ~/soro/teensy-hub
   cd ~/soro/teensy-hub/teensy_ethernet_hub_v2
   ```
   (existing clone: `git submodule update --init`)
3. Build and flash. After a change of `soro_interfaces` (or the first time) rebuild the micro-ROS library:
   ```zsh
   pio run -t clean_microros
   pio run -t upload
   ```

Settings (sensor hardware generation, magnetometer SET/RESET, servo PWM frequency) are in `src/sensor_cfg.h` and `src/servo_cfg.h`. See also [micro-ROS](micro-ROS.md) for trouble shooting the PlatformIO build.

### 2.5 Each session

1. Connect and power the robot (Teensy hub, servos); start Qualisys on the Windows PC if mocap is needed.
2. Terminal 1 – micro-ROS agent:
   ```zsh
   source ~/soro/agent_ws/install/local_setup.zsh
   ros2 run micro_ros_agent micro_ros_agent udp4 --port 8888 -v4
   ```
3. Terminal 2 – hardware nodes (Qualisys bridge + sensor bridge) for the robot in use:
   ```zsh
   ros2 launch soro_bringup hardware.launch.py robot:=soro_6tendon
   ```
   Options: `mocap:=false` (no Qualisys), `apply_calibration:=false` (raw sensor data), `sensor_bridge:=false`.
4. Check:
   ```zsh
   ros2 topic hz /teensy_hub/sensor_data              # ~100 Hz
   ros2 topic echo --once /teensy_hub/board_0/imu_1   # frame_id ..._aligned = calibrated
   ros2 topic hz /soro_5/odometry_naive               # mocap of the tip body
   ```
5. Start the controllers (lab PC or development PC).

Robot configurations (tendon layout, sensors, mocap bodies, calibration) are in [`soro_bringup/robots/`](https://github.com/SoftRobotics-MuM/soro_bringup/tree/main/robots); select one with `robot:=<folder>`.

### 2.6 Sensor calibration

```zsh
ros2 launch soro_bringup calibration.launch.py robot:=soro_6tendon     # mocap:=false if hardware.launch.py runs
ros2 run soro_calibration fit_calibration ~/soro_calib_bags/<run folder>
cp ~/soro_calib_bags/<run folder>/calibration.yaml ~/soro/hardware_ws/src/soro_bringup/robots/soro_6tendon/
cd ~/soro/hardware_ws/src/soro_bringup && git commit -am "soro_6tendon: calibration <date>" && git push
```

`dry_run:=true` runs the sequence with the servos at rest (check of sensors and mocap). The report (`calibration_report.txt` in the run folder) shows the fit quality per sensor.

---

## 3. Development PC (controllers)

### 3.1 Workspace

```zsh
mkdir -p ~/soro/control_ws/src && cd ~/soro/control_ws
git clone https://github.com/SoftRobotics-MuM/soro_bringup.git src/soro_bringup
vcs import --skip-existing src < src/soro_bringup/control.repos
# your own packages: clone them into src/ as well
rosdep install --from-paths src -iy
colcon build --symlink-install
source install/setup.zsh
```

The workspace contains the message definitions (`soro_interfaces`) and the robot configurations (`soro_bringup/robots/`), but none of the hardware packages.

### 3.2 Working with the robot

- The lab PC runs the agent and `hardware.launch.py` (section 2.5); the development PC only needs the same network and `ROS_DOMAIN_ID=72`.
- Check the connection: `ros2 topic list` shows the `/teensy_hub/...` and mocap topics.
- Servo commands: publish `soro_msgs/msg/ServoCommands` on `/teensy_hub/servo_pos` (12 values in µs, 1500 = center, valid range 900–2100). Within a tendon section the sum of the setpoints must stay at 3 × 1500 µs, otherwise the robot would have to shorten (it is too stiff for that).
- Sensor data: `/teensy_hub/board_<i>/imu_<k>` (`sensor_msgs/Imu`) and `/teensy_hub/board_<i>/mag_<k>` (`sensor_msgs/MagneticField`), calibrated in the robot base frame; raw data in `/teensy_hub/sensor_data` (`soro_msgs/SensorArray`).
- Mocap: `/<body>/odometry_naive` (raw) and `/<body>/ground_truth/odometry` (filtered).
- Do not start a second Qualisys bridge on the development PC – it runs on the lab PC.

### 3.3 MATLAB

See [ROS2 in MATLAB](ROS2-in-MATLAB.md). The `soro_msgs` definitions for the custom message build are in `src/soro_interfaces/soro_msgs`.

---

## 4. Maintenance

| task | command |
|---|---|
| update all repositories of a workspace (branches) | `vcs pull src` |
| apply changed pinned versions of the `.repos` file | `vcs import src < src/soro_bringup/hardware.repos` (or `control.repos`) |
| status of all repositories | `vcs status src` |
| record the exact state for an experiment / paper | `vcs export --exact src > experiment_<date>.repos` |
| rebuild after updates | `colcon build --symlink-install` |
| clean rebuild | `rm -rf build install log && colcon build --symlink-install` |

**Changing message definitions** (`soro_interfaces`): tag a new version, update the tag in both `.repos` files and in the `teensy-hub` submodule, rebuild all workspaces and re-flash the firmware (`pio run -t clean_microros && pio run -t upload`). PCs with different message definitions cannot communicate.

## 5. Trouble shooting

| symptom | cause / fix |
|---|---|
| `Package 'soro_bringup' not found` | workspace not sourced in this terminal: `source ~/soro/<ws>/install/setup.zsh` |
| packages from an old workspace are used | it was sourced while building: open a terminal without it, `rm -rf build install log`, rebuild; check that `install/setup.sh` only lists `/opt/ros/jazzy` |
| `CMakeCache.txt directory ... is different` | workspace was moved/renamed: `rm -rf build install log` and rebuild |
| no `/teensy_hub/...` topics | agent not running, wrong lab PC IP (must be 192.168.1.113), firewall blocks UDP 8888, or Teensy not powered |
| topics visible on the lab PC but not on a development PC | different `ROS_DOMAIN_ID`, different subnet or firewall, see [ROS setup trouble shooting](ROS-Setup_trouble-shooting.md) |
| `pio: command not found` | PlatformIO not in `PATH` (section 2.4) |
| Qualisys bridge: "Could not read QTM Config" | harmless false warning of the current upstream version, streaming works |

# Autoware for F1TENTH

This repository contains a modified version of Autoware that works on the F1TENTH car equipped with a Jetson Orion Nano. It uses the `f1tenth_humble` branch with specific fixes for dependency issues, particularly with the geometry2 package.

## Overview

Autoware is an open-source software stack for autonomous driving, based on ROS 2. This adaptation makes it compatible with the F1TENTH platform, which is an autonomous racing platform built around a 1/10 scale RC car.

## Hardware Requirements

- F1TENTH car
- NVIDIA Jetson Orion Nano
- Appropriate sensors (lidar, camera, IMU, etc.)

## Software Prerequisites

- Ubuntu 22.04
- ROS 2 Humble
- CUDA (compatible with Jetson Orion Nano)

## Installation Instructions

### 1. Set up ROS 2 Humble

Follow the official ROS 2 Humble installation instructions for ARM64 architecture:
```bash
sudo apt update && sudo apt install -y software-properties-common
sudo add-apt-repository universe
sudo apt update && sudo apt install -y curl gnupg lsb-release
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(source /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
sudo apt update
sudo apt install -y ros-humble-desktop
```

### 2. Install Development Tools

```bash
sudo apt update && sudo apt install -y \
  build-essential \
  cmake \
  git \
  python3-colcon-common-extensions \
  python3-flake8 \
  python3-pip \
  python3-pytest-cov \
  python3-rosdep \
  python3-setuptools \
  python3-vcstool \
  wget
```

### 3. Create a Workspace

```bash
mkdir -p ~/autoware_ws/src
cd ~/autoware_ws
```

### 4. Clone the Repository

```bash
cd src
git clone -b f1tenth_humble https://github.com/pranavk-2003/AutowareForF1tenth.git
```

### 5. Install Dependencies

Initialize rosdep if you haven't already:
```bash
sudo rosdep init
rosdep update
```

Install dependencies:
```bash
cd ~/autoware_ws
rosdep install -y --from-paths src --ignore-src --rosdistro humble
```

### 6. Build the Workspace

Build with optimizations for the Jetson:
```bash
cd ~/autoware_ws
colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release
```

### 7. Source the Setup File

```bash
source ~/autoware_ws/install/setup.bash
```

## Known Issues and Fixes

### geometry2 Package Dependencies

The original geometry2 package may have compatibility issues with the F1TENTH platform and Jetson Orion Nano. The following fixes have been applied in this repository:

1. Updated CMake minimum version requirements
2. Fixed Python dependency issues in tf2_ros_py
3. Modified package.xml files to accommodate ARM64 architecture
4. Addressed thread safety issues in transform listener implementations

### Resource Optimization for Jetson

Several components have been optimized to work within the resource constraints of the Jetson Orion Nano:

1. Reduced point cloud processing density
2. Optimized perception modules for lower computational load
3. Modified planning parameters for the F1TENTH scale

## Configuration

### Sensor Configuration

Modify the sensor kit configuration files in `sensor_kit/` directory to match your specific sensor setup:
```bash
# Example: Edit the LiDAR configuration
nano ~/autoware_ws/src/sensor_kit/sample_sensor_kit_launch/config/lidar.param.yaml
```

### Vehicle Parameters

Adjust the vehicle parameters to match your F1TENTH car:
```bash
nano ~/autoware_ws/src/vehicle/sample_vehicle_launch/config/vehicle_info.param.yaml
```

## Running Autoware

Launch the basic stack:
```bash
ros2 launch autoware_launch autoware.launch.xml vehicle_model:=f1tenth_vehicle sensor_model:=sample_sensor_kit
```

## Contributing

If you find additional issues or have improvements, please submit a pull request or open an issue on the GitHub repository.

## License

This project is licensed under the Apache License 2.0 - see the individual package LICENSE files for details.

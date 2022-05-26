# Robothon 2022
Repository of Team "Wall-E 3.0"

# Equipment Used List
- Franka Emika Panda Robot
- Intel RealSense D435i
- Standard PC with Ubuntu 18.04 (no GPU!)
- Spotlight for repeatable light conditions
- 3D-printed robot attachment for extracting the batteries incl. magnet
- 3D-printed taskboard holder, which holds the taskboard always in the same place after it was pushed in from a random position. Springs prevent the taskboard from moving
- 3D-printed fingers (PETG) with rubber on surface
- Wooden plate as working space
- Checkerboard (printed)
- Charuco Marker (printed)
- Metal Tip (for Hand-Eye-Calibration)

# Software Dependency List
#### Robot (C++)
- Ubuntu 18.04
- Kernel: 5.4.102-rt53
- gcc/g++ 7.5.0
- cmake 3.10.2
- C++17
- ROS Melodic
- Eigen
- franka_ros
- ros-planning / geometric_shapes
- moveit
- rviz
- libfranka

#### Vision (Python)
- Intrinsic camera calibration: ROS camera_calibration
- Tip calibration: custom implementation
- Hand-eye Calibration: ROS easy_handeye
- Hand-eye Calibration Optimization: custom implementation
- 6D Object Pose Estimation of Taskboard: custom implemenation based on ROS planar_pose

# Quick Start Guide
## Build
1. Install *ROS Melodic* as described [here](http://wiki.ros.org/melodic/Installation/Ubuntu).
1. Make sure you have the most up to date packages:
```bash
rosdep update
sudo apt-get update
sudo apt-get dist-upgrade
```
1. For the later *MoveIt!* source build, the following tools are required:
` sudo apt install python-wstool python-catkin-tools clang-format-10 python-rosdep `
1. Install *catkin*: `sudo apt-get install ros-melodic-catkin python-catkin-tools`
1. Install *Eigen*: `sudo apt install libeigen3-dev`
1. Install *libfranka*: `sudo apt install ros-melodic-libfranka`
1. Install `clang` tools: `sudo apt install clang-tools-6.0 clang-format-6.0 clang-tidy**-6.0`
1. Some controllers might be missing, thus run: `sudo apt-get install ros-melodic-ros-control ros-melodic-ros-controllers`
1. Clone the project with all its submodules: `git clone --recursive [our private repository]`
1. Configure the *catkin* workspace and build the project:
```bash
cd robothon2022/catkin_ws
source /opt/ros/melodic/setup.bash
rosdep install -y --from-paths src --ignore-src --rosdistro ${ROS_DISTRO}
catkin config --extend /opt/ros/${ROS_DISTRO} --cmake-args -DCMAKE_EXPORT_COMPILE_COMMANDS=ON -DCMAKE_BUILD_TYPE=Release
catkin build
```


## Initial start-up after building the project
1. Go to the `./robothon2022/catkin_ws` folder and start a new terminal from there. (Source the *catkin* workspace in **every** new terminal: `source devel/setup.bash`)
2. To run the robothon script, run:  
`roslaunch state_machine run_application.launch app:=<name_of_the_application_target> vision:=<boolean> order:=<tasks_to_be_done>`  

`order` corresponds to the tasks which should be executed. they can be ordered as you like and tasks can be omitted. (1=Blue Button Task; 2=Key Task; 3=LAN Task; 4=Button Battery Task; 5=Lid Open Task; 6=Battery Task; 7=Red Button Task)

E.g.: `roslaunch state_machine run_application.launch app:=robothon vision:=true order:=1,2,3,4,5,6,7`  
The *robothon* target is the default that will be run, if the `app` is not provided. The vision  is set to `false` by default. All tasks are executed if no `order` argument is given.

3. Open three more sourced (!) terminals in `./robothon2022/catkin_ws` and run:

`roslaunch realsense2_camera rs_camera.launch filters:=pointcloud ordered_pc:=true align_depth:=true`

`rosrun planar_pose object_detection.py`

4. When the robot is at the "detection pose" (does not move anymore) start the the following Python file:

`rosrun planar_pose planar_pose_estimation.py`

This file will start publishing pose estimations. In the meanwhile the robot is still waiting for these published topics. As soon as the robot receives the pose estimations, the robot starts moving.

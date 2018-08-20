# Sawyer Control
## Description
Sawyer Control is a repository that enables RL algorithms to control Rethink Sawyer robots via an OpenAI Gym Style interface. It is both a ROS package and a set of Sawyer (Gym) Envs combined in one. The ROS portion of the repo handles the actual control of the robot and is executed in Python 2. The environments are all in Python 3, and communicate to the robot via the ROS interface. Currently, this repo is capable of utilizing both the state information of the robot as well as visual input from a Microsoft Kinect sensor. 

## Setup Instructions:
1. Make sure ros kinetic is installed and make sure to add source /opt/ros/kinetic/setup.bash to your bashrc
2. Install intera interface from the rethink website and set up intera.sh with the correct ip and hostname of your robot
3. Git clone the following in ~/catkin_ws/src/:
* Urdfdom: https://github.com/ros/urdfdom.git
* urdf_parser_py: https://github.com/ros/urdf_parser_py
* pykdl utils: https://github.com/gt-ros-pkg/hrl-kdl/tree/
4. switch to the indigo-devel branch on all three repos
5.`git clone sawyer_control: https://github.com/mdalal2020/sawyer_control.git` in ~/catkin_ws/src/
6. run `catkin_make`
7. Make sure you are on system python
8. run `pip install -r system_python_requirements.txt`
9. install anaconda 2 (Do not install anaconda 3!) and type no when it asks you prepend the anaconda path to the bashrc
10. manually add in the anaconda path to the bashrc (see example bashrc below)
11. run `conda create -n <env_name> python=3.5 anaconda`
12. source activate your python 3 conda environment
13. run `pip install -r python3_requirements.txt`
14. install kinect2 bridge: https://github.com/code-iai/iai_kinect2/tree/master/kinect2_bridge

Example Bashrc:
```
source /opt/ros/kinetic/setup.bash
source ~/catkin_ws/devel/setup.bash
export PATH="$PATH:$HOME/anaconda2/bin"
export PYTHONPATH=$PYTHONPATH:/opt/ros/kinetic/lib/python2.7/dist-packages/:
```
Useful aliases:
```
alias saw="cd ~/catkin_ws/; ./intera.sh; cd ~/"
alias enable="rosrun intera_interface enable_robot.py -e"
alias disable="rosrun intera_interface enable_robot.py -d"
alias reset="rosrun intera_interface enable_robot.py -r"
alias stop="rosrun intera_interface enable_robot.py -S"
alias status="rosrun intera_interface enable_robot.py -s"
alias exp_nodes="roslaunch ~/catkin_ws/src/sawyer_control/exp_nodes.launch"
alias kinect="roslaunch kinect2_bridge kinect2_bridge.launch"
```

## Usage:

### Basic workflow:

1. Make sure robot is enabled

2. open a new terminal and run the following commands:
``` 
saw
exp_nodes 
``` 
3. (Optional) open another terminal/tab and run the following commands:
```
saw 
kinect
```
3. Now open another terminal/tab and run your algorithm on the sawyer

In terms of running algorithms on the sawyer, you can simply plug in the sawyer environments directly into your launch script in the same way you use any other gym environment: The sawyer environments follow the OpenAI Gym API. Additionally, these environments are Multitask by default, which means that they resample goals on reset and have goal conditioned rewards. If you just want a single goal, set `fix_goal=True` and provide the goal you desire to the environment in `fixed_goal`.  

### Configs:
All the important/hardcoded settings for robot/env details are stored in the config files. Please do not change the ros_config or base_config files. If you wish to modify settings, make a new configuration file and have it import all the standard configs and modify the rest, see `austri_config.py` for an example. Then add the file to the config dictionary in `config.py` and simply pass in the name to the env to obtain the desired settings. 

### Common Problems:
The robot doesn't move!

A: Double check that you ran `exp_nodes` before running the experiment


I ran `exp_nodes` and the robot still doesn't move!

A: run `status` and check if `Ready=False` if so the robot is in homing mode (Don't currently have a consistent fix for this)


The arm is just moving upwards all the time

A: You probably need to up the `torque_action_scale`, I recommend something like 4/5, but it depends on how safe you want the environment and how constrained your space is. The problem occurs because you are applying too small torques, so the solution is to apply larger torques. 


## Features:
* Torque and Position Control Modes
* End Effector Position Reaching Environment
* Vision Wrapper 
* Gym-Style Interface 

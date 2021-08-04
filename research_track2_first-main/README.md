# research_track2_first
# Research Track 2 - first assignment

Shahrzad Eskandari Majdar 5060737

  launch->    launch files (
    sim.launch            . Gazebo simulation
    sim_bridge.launch     . launch python scripts
    sim_coppelia.launch   . launch nodes )  
    
  scripts->       - python scripts
(    go_to_point.py        . controlling the robot
    user_interface.py      . minimal command line UI )
    
  src->        - C++ source code
(    position_service.cpp  . returns random position
    state_machine.cpp    . manages the FSM logic )
    
  srv->    - custom services
(    Command.srv           . user UI input
    Position.srv           . goal position
    RandomPosition.srv     . bound random pose )
    
  urdf->  - robot description for Gazebo simulation
(    my_robot.urdf         . mobile robot description)

  cplScenes-> - CoppeliaSim scenes
 (   pioneer_scene.ttt     . Pioneer p3dx scene 
    robotnik_scene.ttt     . Robotnik Summit XL140701 scene )

##Decription of the packages

This package controls a mobile non-holonomic robot with a simple 'go_to_point' behaviour:
1. a random goal is issued (a _pose_, [x,y,theta]);
2. the robot orients itself towards the [x,y] destination;
3. it then drives straight to that position (adjusting the orientation if need be);
4. having reached the [x,y] goal position the robot turns in place in order to match the goal _theta_;
5. if the user does not stop the robot GOTO step 1, otherwise stay still until asked to start again, then GOTO step 1;

Because the user request is implemented as a service, it cannot be preempted; therefore, the robot can only be stopped once a goal has been met, and it must be restarted when a new goal is issued.

## contents

This package provides four nodes that handle the aforementioned mobile robot control. Python scripts are used to implement two nodes.
- **go_to_point.py**: the service server managing the robot speed control depending on the goal received.
- **user_interface.py**:  the simple command line user interface, which sends the requests to start/stop the go_to_point behaviour.

 the last two are C++ based nodes
- **position_service.cpp**: the server generating a random pose [x,y,theta] as a response to a request.
- **state_machine.cpp**:  the FSM managing the request of a new pose when needed, sending it as a goal request to 'go_to_point' service server.

Finally, the control can be applied to a robot simulated using Coppeliasim (see **Requirements**), for which two scenes are here presented
- **pioneer_scene.ttt**: a simple scene with a Pioneer p3dx non-holonomic mobile robot in an empty environment.
- **robotnik_scene.ttt**: a simple scene with a Robotnik Summit XL140701 non-holonomic mobile robot in an empty environment.

## Compiling and Running

Compilation can be carried out as always with

$catkin_make

" $ roslaunch rt2_assignment1 sim.launch "

In this case the Gazebo simulation will automatically start.
 " $roslaunch rt2_assignment1 sim_coppelia.launch "

CoppeliaSim must be started individually in this situation as a reminder must have  roscore & running before launching the CoppeliaSim executable.
The simulation can be started before or after the nodes are launched, however if you try to run a new simulation after the nodes have already been launched on a prior instance, the system may end up in a different initial state than expected, and you will never be able to attain the goal. In other words, the nodes should be restarted every time the simulation is resumed, and vice versa .

" $ roslaunch rt2_assignment1 sim_bridge.launch "
## Requirements

**Gazebo** is required to run the first launch file (the scene definition is presented in this package).
**Coppeliasim** is required to run the second launch file [download link](http://www.coppeliarobotics.com/downloads.html)
**ros1_bridge** ROS2 package, as well as the **ros2** branch of this repository cloned in a ROS2 workspace in order to run the third launch file (again, it's _strongly_ suggested to read the README on that branch for further information).


## Known Issues and Limitations
If you try running both the Gazebo and CoppeliaSim and the latter seems to not respond to the nodes, whilst the user interface results frozen after having told the system to run, try to **kill the roscore process**; this might be related to Gazebo overwriting some values related to the simulation (probably simulation time) and these not being appropriately "cleaned" once Gazebo is closed.







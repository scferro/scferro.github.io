---
name: Pour Over Coffee Using a 7DOF Robot Arm
tools: [ROS2, Python, OpenCV, Moveit, April Tags]
image: https://scferro.github.io/assets/coffee.gif
description: Programmed a Franka Emika 7DOF robot arm to brew a cup of pour over coffee.
---

# Making Pour Over Coffee with a 7DOF Robot Arm - Botrista
<br>

### Overview 
The goal of this project was to control a robot arm to brew a cup of pour over coffee. An Intel RealSense camera and OpenCV were used to detect the target coffee cup and the various objects the robot needed to brew the coffee. The robot could then pick up each object in order and perform an action with it. The steps to make the coffee were as follows:

1. Wait for a coffee cup to be placed in the cup holder.
2. Place the filter in the coffee pot.
3. Pick up the coffee grounds and dump them in the filter.
4. Pick up the kettle of hot water and pour water into the coffee pot.
5. After coffee finishes dripping, remove the filter.
6. Pour the coffee into the detected cup.

ROS 2 and MoveIt 2 were used to interface with the Franka and control it's movements. A custom wrapper package for MoveIt was written to simplify usage. This package contained services and actions that could be used for a variety of robot motions, such as moving smoothly between two points. 


### Video Demo
<iframe width="640" height="360" src="https://www.youtube.com/embed/INRJ8Y_SD4U?si=X1NxuWG7hQWI9v2w" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


### Project Team 
I worked on this project as part of a team. My focus was on object localization and grasping, planning trajectories for objects while holding them level (particularly the coffee ground scoop), and the design and manufacturing of 3D printed parts (robot gripper, handles for various objects, etc.). A big thank you to the other members of my group, Anuj Natraj, Carter DiOrio, Kyle Wang, and Jihai Zhao.


### System Setup
A Franka Emika seven degree of freedom robot arm was used for this project. In addition, two Intel RealSense cameras were used to track the location of items in the environment. The first camera was a d435 was mounted above the robot, with a full view of the workspace. This camera was used for determining the rough positions of the various objects and the robot arm by detecting April tags. It was also used for detecting when the cup is placed in the cup holder, which starts the coffee making routine. 

The second camera was a d405 mounted directing to the robot arm with a view of the gripper. This camera was used for detecting the position of object handles before grasping them. 


### Object Localization and Handle Detection


### Grasping


### Tasks


### Lessons Learned and Future Work:
- The biggest issues we encoutered with this project was moveit getting the robot into configurations that it could not get itself out of. This happened most frequently with motions that had a lot of path or goal constraints, such as moving the scoop to the pot or pouring the pot into the cup. We were able to work around these issues largely by tuning the constraints and by inserting more known intermediate positions for the robot to go to before reaching the goal pose. One idea to further improve this issue would be to have moveit find multiple paths to the goal, then choose the best option, rather than just picking the first solution it finds with RRT. Perhaps it would also be possible to write code allow moveit to take into account future commands as well as the current command. This could allow it to plan a path from which it does not get stuck. 
- Another issue we encountered was the weight of the kettle. Though we were somewhat close to the Franka's payload limit, we expected that it should have been able to carry more water than it was able too. If we over filled the kettle, the robot would sometimes fail to pick up the kettle because it thought it had collieded with something. Perhaps we could slightly increase payload by further tuning the Franka payload variables.
- Additionally, there are some additional action the robot could complete the would make for a more full-featured demo. We wanted the robot to be able to scoop it's own coffee grounds, turn the kettle on and boil water by itself, and throw away old coffee grounds. With these features, as long as there were still coffee grounds in the jar and water in the kettle, the demo would immediatly be able to run again after making a cup of coffee. 

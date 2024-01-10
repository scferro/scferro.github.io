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
<iframe width="800" height="450" src="https://www.youtube.com/embed/INRJ8Y_SD4U?si=X1NxuWG7hQWI9v2w" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<br>


### Project Team 
I worked on this project as part of a team. My focus was on object localization and grasping, planning trajectories for objects while holding them level (particularly the coffee ground scoop), and the design and manufacturing of 3D printed parts (robot gripper, handles for various objects, etc.). A big thank you to the other members of my team, Anuj Natraj, Carter DiOrio, Kyle Wang, and Jihai Zhao.


### MoveIt Wrapper
The make interacting with MoveIt easier, we wrote a custom wrapper interface to use in this project. Using the wrapper meant that planning trajectories, setting constraints, grasping, etc. could all be done with a couple simple function calls. This eliminated a lot of redundant code from the ROS nodes. 


### System Setup
A Franka Emika seven degree of freedom robot arm was used for this project. In addition, two Intel RealSense cameras were used to track the location of items in the environment. The first camera was a d435 was mounted above the robot, with a full view of the workspace. This camera was used for determining the rough positions of the various objects and the robot arm by detecting April tags. It was also used for detecting when the cup is placed in the cup holder, which starts the coffee making routine. The second camera was a d405 mounted directing to the robot arm with a view of the gripper. This camera was used for detecting the position of object handles before grasping them. Below is a picture of the d405 mounted on the robot.

<img src="{{ site.url }}{{ site.baseurl }}/assets/coffee_cam.jpeg" width="500"/>
<br>


### Object Localization/April Tags
There were several different objects placed around the object that it would need to pick up to make coffee - the coffee filter, the scoop, the kettle, and the coffee pot. To start, each object was placed on a separate pad with an April tag. In addition, one April tag was fixed to the table with a known transform to the base of the robot. All of these April tags could be seen by the ceiling camera, which could then determine (1) the transform from the camera to the main April tag on the table, (2) the transform from the main April tag to the base of the robot, and (3) transforms from the main April tag to all the other April tags on various mats for the different objects. From this, the robot knows the rough position of every object, relative to it's own location. Below is an photo of the robot workspace, showing the object mats and various April tags. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/coffee2.JPEG" width="500"/>
<br>


### Handle Detection
Once the robot knows the rough position of each object using the April tags, it then refines it's estimate of the handle positions using the d405 camera mounted on the robot. First, the robot moves into position above the April tag, high enough where the handle of the object is in view. The handles of the various objects were wrapped with two colors of tape - green tape was used closer to the center of the object and blue tape was used further from the center. The robot sees the green and blue regions and calculates a vector from the centroid of the blue are to the centroid of the green area. This vector also points from the handle towards the center of the object. 

Once the robot detects the handle vector, it moves into position at a fixed offset from the detected handle. It orients itself so the handle vector is 90 degrees to the gripper. It performs two refinement movements to be certain that the handle is fully visible by the camera and that the system has identified the correct handle position. Once the refinement was complete, the robot would move it and grasp the object. Below are two screenshots from the d405 camera during the handle detection process. The first is for the kettle and the second is the coffee scoop. 


### Grasping
In order to securely grasp these objects, especially the relatively heavy ones like the kettle, we designed a custom gripper for the Franka as well as custom handles for the various objects being picked up. Rather than a flat grasping surface, our gripper had large diamond-shaped notches cut into it. The custom handle we made for each object also had the same diamond shape, which allowed the robot to very securely grasp each object. The gripper also incorporated a flat section, so it could still be used to pinch objects to pick them up too. This was used for picking up the coffee scoop, which was too small for a custom handle. Below are photos of the gripper being used on different handles. 


### Lessons Learned and Future Work
The biggest issues we encountered with this project was MoveIt getting the robot into configurations that it could not get itself out of. This happened most frequently with motions that had a lot of path or goal constraints, such as moving the scoop to the pot or pouring the pot into the cup. We were able to work around these issues largely by tuning the constraints used and by inserting more known intermediate positions for the robot to go to before reaching the goal pose. One idea to further improve this issue would be to have MoveIt find multiple paths to the goal, then choose the best option, rather than just picking the first solution it finds with RRT. Perhaps it would also be possible to write code allow MoveIt to take into account future commands as well as the current command. This could allow it to plan a path from which it does not get stuck. 

Another issue we encountered was the weight of the kettle. Though we were somewhat close to the Franka's payload limit, we expected that it should have been able to carry more water than it was able too. If we over filled the kettle, the robot would sometimes fail to pick up the kettle because it thought it had collided with something. Perhaps we could slightly increase payload by further tuning the Franka payload variables.

Additionally, there are some additional action the robot could complete the would make for a more full-featured demo. We wanted the robot to be able to scoop it's own coffee grounds, turn the kettle on and boil water by itself, and throw away old coffee grounds. With these features, as long as there were still coffee grounds in the jar and water in the kettle, the demo would immediately be able to run again after making a cup of coffee. 

<p class="text-center">
{% include elements/button.html link="https://github.com/scferro/botrista_ferro" text="GitHub" %}
</p>

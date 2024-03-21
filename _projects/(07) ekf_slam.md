---
name: EKF SLAM Algorithm from Scratch
tools: [C++, ROS 2, SLAM, Extended Kalman Filter]
image: https://scferro.github.io/assets/ekf_slam_main.gif
description: Created a collection of ROS 2 packages for a turtlebot to create a map, detect and associate obstacles, and localize itself. 
---

# EKF SLAM Algorithm from Scratch
<br>

### Overview
In this project, I implemented a SLAM algorithm using an extended Kalman filter from scratch on a Turtlebot3. I also created a simulation environment using ROS2 and RViz for testing and tuning robot control and the SLAM algorithm. 

### Video Demo
Below is a demonstration of the the NuSLAM package running on the Turtlebot 3 burger with tele-op control. The robot is able to fairly accurately detect obstacles and track it's position using the lidar data, especially when it is within the cluster of obstacles. As the robot drives further away, it qucikly loses track of it's position. However, once it returns to the cluster, it is able to relocate itself. I even crashed the robot into a fixed obstacle and continued driving to throw off the odometry position estimate, but the robot was still able to relocate itself fairly accurately.

<iframe width="800" height="450"
src="https://www.youtube.com/embed/rpW0WM7pRoI"
title="YouTube video player"
frameborder="0"
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
allowfullscreen></iframe>
<br>


### Robot Control and Odometry
I created a C++ library dedicated to managing 2D transformation matrix calculations, along with both forward and reverse kinematics for a differential drive robot. This library is pivotal for translating a target robot velocity (indicated by the cmd_vel topic) into specific commands for the wheels. It also transforms encoder reading updates into the robot's updated position through odometry.


### Localization and Mapping
I implemented a real-time extended Kalman filter SLAM that works with distinct features. This method monitors a robot's location in 2D (x, y, θ) and the positions of "n" map features, visualized as cylinders located at specified (mxi, myi) coordinates. Each cycle, the robot's new state and covariance is calculated using wheel odometry data. Subsequently, with every obstacle detected, the estimates for the map, the robot's status, and its covariance are refined using the new data. This approach significantly improves upon mere odometry in both simulated environments and actual settings.


### Perception Algorithm 
First, I implemented obstacle detection, which detects groupings of lidar scan points that are very close together. Next, I carried out circle regression and classification on these clusters to determine if they were valid cylindrical obstacles that should be tracked by the robot. This involves calculating the center of a cluster of points and then deciding if it qualifies as a circle based on radius filtration and the Variance of Inscribed Angle.

Finally, I performed data association. This process identifies whether a detected circle is a new obstacle to be or if it should be linked with one that was previously identified. This is done by computing the Euclidian distance between the newly detected circle and each known obstacle to identify the closest one. If this closest distance exceeds a certain limit, the circle is considered a new obstacle; otherwise, it is associated with the nearest obstacle.


### Simulation
The video demonstrates my comprehensive SLAM algorithm functioning within a simulation. The odometry estimates are represented by a blue robot, whereas the green robot and obstacles depict the SLAM estimates. Red robots and obstacles illustrate the “true” simulated locations. As time progresses, the SLAM-derived estimate consistently aligns with the simulated robot's position more closely than the odometry-based estimates do.

<iframe width="800" height="450"
src="https://www.youtube.com/embed/jyUSyTG43Zc"
title="YouTube video player"
frameborder="0"
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
allowfullscreen></iframe>
<br>


<p class="text-center">
{% include elements/button.html link="https://github.com/scferro/ekf_slam_project" text="GitHub" %}
</p>

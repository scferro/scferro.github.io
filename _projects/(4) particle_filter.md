---
name: Implementation and Assessment of a Particle Filter 
tools: [OpenCV, Python, SIFT, Motion Estimation]
image: https://jiasenzheng.github.io/assets/vo.gif
description: Applied a particle filter to a robot dataset to improve the position estimate of the robot. 
---

# Implementation and Assessment of a Particle Filter 
<br>

<img src="{{ site.url }}{{ site.baseurl }}/assets/particle_filter_5.png"/>


### Overview
The goal of this project was to implement a particle filter on a set of data collected from a robot in a test environment. Odometry data containing the commanded linear and angular velocities, distance and heading measurements relative to known landmarks, and ground truth position data for the robot were all provided as part of the data set. The data was collected from robots at the Autonomous Space Robotics Lab at the University of Toronto. Thorough documentation of the robot platform, task and file formats, as well as video, are provided here: http://asrl.utias.utoronto.ca/datasets/mrclam/index.html.

Overall, the particle filter was successfully able to combine the odometry data and the measured position data to dramatically improve the position estimate relative to the pure dead reckoning estimate. As expected, the filtering algorithm still struggled in situations where no measurements were taken. However as long as measurements were taken regularly, the filter was able to keep the position estimate within ~0.25m of the ground truth position. 


### Results and Assessment of Filter Performance
<img src="{{ site.url }}{{ site.baseurl }}/assets/particle_filter_1.png"/>
<img src="{{ site.url }}{{ site.baseurl }}/assets/particle_filter_2.png"/>
<img src="{{ site.url }}{{ site.baseurl }}/assets/particle_filter_3.png"/>
<img src="{{ site.url }}{{ site.baseurl }}/assets/particle_filter_4.png"/>

### Dead Reckoning Motion Model
The dead reckoning position estimate based on simple trigonometry and physics. The inputs for the model are the linear velocity and angular velocity (omega) commands provided in the odometry data file, along with a time interval between commands. 

First, the program determines whether the current command is for a linear movement or not based on the angular velocity. If angular velocity equals zero, the movement is assumed to be linear. For linear motion, the angle of the robot relative to the environment (theta) does not change. The distance the robot travels is simply calculated using the velocity and time interval.

    distance = vel * time

For a non-linear movement, where angular and linear velocity are not zero, the robot is assumed to be moving in a curved path. Linear and angular velocity are considered to be constant for each robot movement, and moving with a constant linear and angular velocity will result in movement in a circular path. To determine the position of the robot after executing the command, a radius of curvature is calculated using the equation below. 

    radius = abs(vel / omega)

The change in the robot's angle relative to the environment is calculated by multiplying the the angular velocity by the time interval. This is then added to the current theta angle to find the new theta angle. 

    Δtheta = omega * time

    theta_new = theta_current + Δtheta

Next, the center of the curve is calculated using the equations below. The curve center is assumed to be one radius to the left or right of the robot, depending whether the robot is turning to the right or left. 

    Left Hand Turn:
    curveCenterX = x_current + radius * cos(theta + pi/2)
    curveCenterY = y_current + radius * sin(theta + pi/2)

    Right Hand Turn:
    curveCenterX = x_current + radius * cos(theta - pi/2)
    curveCenterY = y_current + radius * sin(theta - pi/2)

Finally, new x and y coordinates are calculated using the equations below. Essentially, these equations determine the robot’s position along the curve with the center and radius calculated above based on the calculated Δtheta. 

    Left Hand Turn:
    x_new = curveCenterX + radius * cos(theta_new)
    y_new = curveCenterY + radius * sin(theta_new)

    Right Hand Turn:
    x_new = curveCenterX - radius * cos(theta_new)
    y_new = curveCenterY - radius * sin(theta_new)


### Measurement Model
The measurement model uses trigonometry to estimate the distance and heading from a specified state of the robot to a known landmark position. Within the particle filter, it is used to estimate the distance and heading from any given particle to a landmark and a specified x-y position. 

The inputs for the model are the current state of the robot or particle (x, y, and theta) and the position of the landmark (x and y). To calculate the distance between the robot state and the landmark, Pythagorean's theorem is used. To calculate the heading angle from the robot to the landmark, phi, in the global coordinate system, the arctangent of the y distance between the robot and the landmark over the x distance between the two is used. The robot orientation theta is then subtracted to convert the heading from the global coordinate system to the robot’s coordinate system, as that is what the robot would report if it took this measurement. The full equations for both calculations are below. 

    dist = sqrt((x_robot - x_landmark)**2 + (y_robot - y_landmark)**2)

    phi_global = atan((y_robot - y_landmark)/(x_robot - x_landmark))

    phi_robot = phi_global - theta

The outputs are the distance from the robot to the landmark and the heading of the landmark relative to the robot frame, phi_robot. This is the same data that is reported by the sensor system on the robot, so the outputs from this model can be compared to a current measurement from the sensor to evaluate the current position of the robot. 


### Implementation of Particle Filter

1. First, an initial belief state is generated. In the particle filter, the belief state is comprised of a set of particles, X_t. Each particle represents a possible state of the robot. In this project, the state variables are the robot coordinates x and y and the orientation theta. The initial particle set can be generated using a uniform distribution across the entire workspace, a random distribution, or they can be generated based on the initial robot state, if it is known. The initial state is be called X_t−1. The number of particles (M) is specified as part of the filter design. 

2. Next, the robot will take a measurement to estimate its state. The measurement, z_t, is used to calculate the importance factor for each particle in X_t−1. The filter will then estimate the expected measurement for each particle using the measurement model. Then, it will compare the expected measurement for each particle to the measurement z_t. Particles whose estimates more closely match the measurement are assigned high probabilities, while particles that do not match are assigned low probabilities. These probabilities are called importance factors, ω_t.  The state of each particle, along with the importance factor, is then added to a new matrix 
X′_t. 

3. Then, the particle set is re-sampled based on the importance factors. From the particle set X′_t, particles are sampled randomly with the importance factors as weights (i.e. particles with high importance factors are more likely to be selected). The selected particles should be added to the new belief state X_t. There will be redundant particles, as particles with high importance factors will be sampled more than once. This new particle set should have a distribution with more particles in the most probable states as a result of this resampling step. This resampling is critical for estimating the robot's state successfully.  

4. Finally, wait for a new measurement to repeat the process. X_t is now the current belief state is now. While waiting for a new measurement, X_t may still be updated based on inputs to the robot. In this project, the state of every particle in the belief state is updated between measurements based on the odometry input data provided between the measurements and using the motion model. When a new measurement z_t is collected, X_t becomes X_t−1 and the process repeats.  

<p class="text-center">
{% include elements/button.html link="https://github.com/scferro/me469_hw0" text="GitHub" %}
</p>
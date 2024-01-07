---
name: Design and Testing a 3D Printed Cycloidal Robot Actuator
tools: [3D Printing, CAD, Test Engineering, Mechanical Design]
image: https://scferro.github.io/assets/actuator_main.png
description: Designed and tested a 3D printed cycloidal actuator for use in walking robots.
---

# Design of a 3D Printed Cycloidal Robot Actuator for a Quadruped Robot
<br>

<img src="{{ site.url }}{{ site.baseurl }}/assets/particle_filter_5.png"/>


### Overview
The goal of this project was to use a particle filter to improve the position and heading estimate for a robot in a test environment. Odometry data containing the commanded linear and angular velocities, distance and heading measurements relative to known landmarks, and ground truth position data for the robot were all provided as part of the data set. The data was collected from robots at the Autonomous Space Robotics Lab at the University of Toronto. Thorough documentation of the robot platform, task and file formats, as well as video, are provided here: http://asrl.utias.utoronto.ca/datasets/mrclam/index.html.

Overall, the particle filter was successfully able to combine the odometry data and the measured position data to dramatically improve the position estimate relative to the pure dead reckoning estimate. As expected, the filtering algorithm still struggled in situations where no measurements were taken. However as long as measurements were taken regularly, the filter was able to keep the position estimate within ~0.25m of the ground truth position. 


<p class="text-center">
{% include elements/button.html link="https://github.com/scferro/me469_hw0" text="GitHub" %}
</p>
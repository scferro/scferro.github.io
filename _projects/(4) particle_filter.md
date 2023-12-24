---
name: Implementation and Assessment of a Particle Filter 
tools: [OpenCV, Python, SIFT, Motion Estimation]
image: https://jiasenzheng.github.io/assets/vo.gif
description: Applied a particle filter to a robot dataset to improve the position estimate of the robot. 
---

# Implementation and Assessment of a Particle Filter 
<br>

### Overview
The goal of this project was to implement a particle filter on a set of data collected from a robot in a test environment. Odometry data containing the commanded linear and angular velocities, distance and heading measurements relative to known landmarks, and ground truth position data for the robot were all provided as part of the data set. The data was collected from robots at the Autonomous Space Robotics Lab at the University of Toronto. Thorough documentation of the robot platform, task and file formats, as well as video, are provided here: http://asrl.utias.utoronto.ca/datasets/mrclam/index.html.

Overall, the particle filter was successfully able to combine the odometry data and the measured position data to dramatically improve the position estimate relative to the pure dead reckoning estimate. As you would expect, the filtering algorithm still struggled in situations where no measurements were taken. However as long as measurements were taken regularly, the filter was able to keep the position estimate within ~0.1m of the ground truth position. 






{% include elements/button.html link="https://github.com/JiasenZheng/Stereo_Visual_Odometry" text="GitHub" %}
</p>
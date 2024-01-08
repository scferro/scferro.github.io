---
name: Robot Arm Pen Grab Challenge
tools: [Python, OpenCV, Robot Kinematics]
image: https://scferro.github.io/assets/pen.gif
description: Programmed a robot arm to detect and grab a pen using computer vision.
---

# Robot Arm Pen Grab Challenge
<br>

### Overview
The goal of this project was to detect the position of a brightly colored pen using computer vision, then grab the pen in space using a robot arm.

<iframe width="560" height="315" src="https://www.youtube.com/embed/NlK7yGbSWw8?si=BYrJMumKhRp9STmD" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<br>

### Hardware Used
**Camera**: Intel RealSense d435i

**Robot**: Interbotix PincherX 100 (4DOF)

### Pen Detection
The pen was detected and tracked using the RGB camera data and depth data provided by the Intel RealSense camera. The camera was placed perpendicular to the robot, in a location where the majority of the robot's workspace was visible to the camera sensor. The pen was a bright purple color, so it's location could be determined using color masking in OpenCV to isolate the purple pixels in the camera's view. A contour were drawn around the purple area, and the centroid of the contour was found. This was considered to be the center of the pen, and would be the target for the robot to grab. The x-y position of the pen centroid in the image was cross-referenced with the depth data from the camera to determine the pen's position in 3D space relative to the camera. 


### Robot Control
First, the robot's position relative to the camera needed to be determined. To do this, the robot holds the pen in a fixed "calibration" pose, while the camera measures the position of the pen centroid. This data is then used to calculate an offset vector from the camera to the base of the robot. Once this step is complete, the robot returns the pen to the user. 

Next, the user positions the pen in front of the robot for detection by the camera. Once the pen is detected and the centroid is calculated, the robot moves into position to grab it, using the offset value found in the calibration step. Because a relatively simple robot arm with only four degrees of freedom was used, and because the orientation of the pen was not important, the desired joint angles were determined using with simple trigonometry. The robot first moves to a "prepare to grab" pose, which is offset slightly from the pen, then it moves in to the "grab" pose and closes the gripper. Finally, the robot swings around and deposits the pen at a predetermined location in front of the user.


<p class="text-center">
{% include elements/button.html link="https://github.com/scferro/pen_challenge" text="GitHub" %}
</p>

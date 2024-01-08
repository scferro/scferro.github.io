---
name: Design and Testing a 3D Printed Cycloidal Robot Actuator
tools: [3D Printing, CAD, Test Engineering, Mechanical Design]
image: https://scferro.github.io/assets/actuator_main.png
description: Designed and tested a 3D printed cycloidal actuator for use in walking robots.
---

# Design of a 3D Printed Cycloidal Robot Actuator for a Quadruped Robot
<br>

<img src="{{ site.url }}{{ site.baseurl }}/assets/actuator_header.png"/>

### Overview
The goal of this project was to develop an actuator for future use in a quadruped robot. The actuator was 3D printed, and used a cycloidal drive to transmit power from a 5008 brushless motor to the output. The design also incorporates a built in encoder for tracking position. It was designed to be economical to produce while still being relatively small and light - these goals were met, with an estimated cost of <$60, a mass of 454g, and ⌀80mm x 67mm size. It was also able to achieve >12 Nm of torque during testing. Based on this success prototypes for a quadruped robot based on this design were built and run through initial testing. 


### Design Targets
The goals for this project were primarily around cost and manufacturability while maintaining some key features for a dynamic walling robot:
- **Cost**: The overall actuator should cost less than $100, not including motor driver
- **Manufacturability**: The actuator should be designed to be largely 3D printed for ease and accessability of manufacturing. Bearings, hardware, electronics, motor can be purchased, but other components should be made using 3D printing,
- **Torque**: Torque output >10 Nm.


### Design
**Cycloidal Gearbox**
A cycloidal gearbox is a mechanical device designed for power transmission, utilizing a cam mechanism with lobed cams and bearings. This design minimizes backlash, ensuring precise torque transmission. It is characterized by its compact size, high torque density, and shock-load resistance. These features make it perfect for this application. 

The cycloidal gearbox operates on the principle of eccentric cams and needle bearings. The input shaft, connected to an eccentric cam, rotates within a lobed cam track. This eccentric motion causes the needle bearings to move along the lobes, resulting in an oscillating, or cycloidal, motion. The output shaft is connected to pins that follows the cycloidal path, transmitting the motion to the output stage. The gearbox's structure inherently minimizes backlash, as the engagement of the bearings with the cam surfaces ensures continuous contact, contributing to its precision and reliability in power transmission applications. Using two cycloidal gears stacked on top of each other and set 180 degrees out of phase can help reduce vibrations from the drive. The second gear helps balance many of the vibrations from the oscillation of the gears. 

Selecting the correct ratio for this gearbox was key. For the dynamics and functionality of the robot, it was important to maintain a back-drivable actuator, where the motor could easily be spun by twisting the actuator. After some preliminary testing of different gears, a ratio of 19:1 was selected. This was the highest tested ratio that could still be easily back-driven. Below is an CAD screenshot of the cycloidal gears used in this project. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/cycloids.png" width="500"/>

**3D Printed Components**
The CAD screenshots below show the 3D printed parts used in this design along with stand ins for some of the purchased parts. Non-printed parts are shown in red. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/actuator_cad.png"/>

Because of the speed and ease of use of 3D printing, I was able to go through many iterations of the design. One area that took significant refinement was the tolerancing of the cams and lobes of the cycloidal gearbox. If the tolerances were too small, the gearbox had excessive torque that made back-driving difficult or impossible. However, if the tolerances were too loose, the actuator would have no precision. Eventually a range was found that satisfied both conditions. It was also noted that the actuators went through a break-in period, where the gearbox became much "looser" as some of the imperfections in the 3D prints were worn down. As a result, new actuators were printed with slightly tight tolerances, and were run through a break in procedure after assembly. 


### Testing


### Future Development
<img src="{{ site.url }}{{ site.baseurl }}/assets/actuator3.png"/>


<p class="text-center">
{% include elements/button.html link="https://github.com/scferro/me469_hw0" text="GitHub" %}
</p>
---
name: Robot Arm Block Sorting with Online Human Feedback
tools: [C++, Python, ROS 2, MoveIt, PyTorch, OpenCV]
image: https://scferro.github.io/assets/blocks_main.gif
description: Created ROS 2 packages to control a robot arm to sort blocks based on human feedback.
---

# Robot Arm Block Sorting with Online Human Feedback
<br>

### Overview

This project aimed to create an intelligent sorting system using a Franka Panda robotic arm. The system's primary goal was to autonomously detect, classify, and sort blocks into two distinct piles based on their visual characteristics. Computer vision techniques and machine learning were combined to process depth and color images, accurately identifying and locating blocks in the robot's workspace. A neural network, capable of both pre-training and online learning through human feedback, made real-time decisions on how to categorize each block. The system's adaptive learning capability allows it to improve its sorting accuracy over time, making it suitable for dynamic environments and changing sorting criteria.


### Demo Video

VIDEO COMING SOON


### Block Detection

<img src="{{ site.url }}{{ site.baseurl }}/assets/franka_sorting_cv.png" />

<br>

The block detection system employed computer vision techniques to accurately detect blocks, their dimensions, positions, and orientations. Key aspects included:

1. Camera Setup:
   - Utilized both color (RGB) and depth information from a d405 RealSense camera. This camera is optimized for close range and high accuracy.
   - Depth data provided 3D positioning of the table in 3d space, while color data aided in block detection and segmentation.

2. Depth Segmentation:
   - Implemented a custom depth segmentation algorithm to isolate the table surface.
   - Used Gaussian blur and Canny edge detection on the depth image.
   - Applied Hough line transform to identify table edges.
   - Created a mask to separate the table surface from the background.

3. Color-based Block Segmentation:
   - Applied color thresholding to isolate potential block regions.
   - Used morphological operations (opening) to reduce noise and refine block shapes.

4. Contour Analysis:
   - Detected contours in the segmented image using cv2.findContours().
   - Analyzed contour properties (area, aspect ratio) to filter out non-block objects.
   - Used cv2.minAreaRect() to find the oriented bounding box of each block.

5. 3D Pose Estimation:
   - Calculated block orientation using the angle of the minimum area rectangle.
   - Estimated block dimensions using the contour's bounding rectangle and depth information.
   - Depth data was used to estimate the height of the block.

This advanced detection system demonstrated robustness to variations in lighting, block orientations, and partial occlusions, providing accurate and reliable block information for the robotic manipulation tasks.


### Neural Network: Online Learning and Pre-training Integration

The block classification system utilized a custom Convolutional Neural Network (CNN) with a focus on online learning, supported by pre-training capabilities. This approach allowed for continuous adaptation during operation while starting from a solid baseline.

**Online Learning System**

* Real-time Adaptation:
   - Developed a dynamic training system integrated with the ROS2 framework.
   - Implemented a service call interface for on-the-fly training requests.
   - Used human feedback to generate labels for newly encountered blocks.

* Training Procedure:
   - Used a supervised learning procedure with Binary Cross-Entropy loss and Adam optimizer.
   - Implemented a learning rate scheduler for adaptive learning rates.
   - Batch size of 1 for true online learning capability.

* Data Management:
   - Maintained a rolling buffer of recent training examples (max 50 images).
   - Implemented a FIFO system to prevent catastrophic forgetting while adapting to new data.

* Adaptive Learning:
   - System continuously updated its model based on sorting decisions.
   - Implemented a confidence threshold to trigger human intervention for uncertain cases.

* Real-time Predictions:
   - Created a ROS2 service for real-time block classification.
   - Implemented batch prediction for multiple views of the same block.
   - Used ensemble averaging of predictions for improved robustness.

**Pre-training Process**

<img src="{{ site.url }}{{ site.baseurl }}/assets/segmented_3.png" />

1. Initial Setup:
   - Initialized the network with random weights.
   - Used a dataset of pre-labeled block images for initial training.
   - The testing dataset was comprised on segmented block images collected while developing the other software features. 
   - Above are some examples of the segmented images collected and used for training.

2. Data Augmentation:
   - Implemented rotations (0°, 90°, 180°, 270°) and mirroring to expand the dataset.

3. Training Approach:
   - Trained for a fixed number of iterations (10) using all available pre-training data.
   - Implemented early stopping based on validation performance to prevent overfitting.

**Integration of Pre-training and Online Learning**

1. Seamless Transition:
   - The pre-trained model served as the starting point for online learning.
   - Online learning began immediately after starting the task, allowing for continuous improvement.

2. Adaptive Behavior:
   - The system could switch between using pre-trained knowledge and newly acquired information.
   - Confidence thresholds determined when to rely on pre-trained knowledge vs. seeking new input.

3. Performance Optimization:
   - Used PyTorch's JIT compilation for faster inference in both pre-trained and online-updated models.
   - Implemented asynchronous processing to minimize impact on real-time control loop.

4. Robustness Enhancements:
   - Incorporated dropout layers to prevent overfitting in both pre-training and online learning phases.
   - The rolling buffer in online learning helped maintain a balance between old and new knowledge.

**Network Architecture**

- Designed a compact CNN using PyTorch, optimized for real-time inference.
- Architecture: 2 convolutional layers, max pooling, 2 fully connected layers, and a sigmoid output.
- Input: 128x128x3 RGB images, Output: Binary classification (0 or 1).

This integrated approach allowed the robot to start with a baseline understanding of block classification from pre-training and continuously improve and adapt its model based on new experiences and human feedback during operation. The seamless integration with ROS2 enabled efficient interaction between the learning system and the robotic control architecture, making the system both adaptive and responsive to new sorting criteria or environmental changes.


### Sorting Process

I leveraged MoveIt to control the Franka Panda robot arm to manipulate the blocks. The custom C++ node interfaced with MoveIt to manage complex arm movements and gripper actions. Here's a detailed breakdown of the implementation:

1. MoveIt Integration:
   - Utilized MoveIt's C++ API to create a MoveGroupInterface for the Franka Panda arm.
   - Implemented collision-aware motion planning using MoveIt's PlanningSceneInterface.
   - Set up custom planning parameters including planning time, velocity scaling, and acceleration scaling.

2. Block Scanning and Detection:
   - Defined an "overhead_scan_pose" for the initial block detection phase.
   - Used MoveIt to plan and execute a trajectory to this pose.
   - Triggered the computer vision system to detect and localize blocks.

3. Pick Operation:
   - Moved to a pre-grasp pose slightly above each detected block.
   - Planned and executed a linear motion to the grasp pose.
   - Closed the Franka gripper using a custom ROS2 action client.
   - Planned and executed a linear motion upwards to lift the block.
   - Place operations essentially follow the opposite procedure.

4. Sort Decision:
   - While holding the block, moved to a "decision pose" for better imaging.
   - Triggered the neural network for classification via a ROS2 service call.
   - Based on the classification result, determined the target pile for placement.
   - Implemented low-confidence handling, where human input was requested for uncertain classifications.

5. Collision Avoidance:
   - Dynamically updated the planning scene with placed blocks as collision objects.
   - Implemented custom collision checking for the gripper when approaching blocks.


The full sorting process followed this sequence:
1. Initial scan of the workspace.
2. Select the next unsorted block.
3. Plan and execute pick operation.
4. Move to classification pose and get neural network prediction.
5. Plan and execute place operation based on classification.
6. Update planning scene with new block position.
7. Repeat until all detected blocks are sorted, then wait for new blocks to be introduced.

This implementation demonstrated the powerful integration of MoveIt's planning capabilities with the Franka Panda's precise control, enabling robust and adaptive sorting behavior. The system successfully handled variations in block positions, adapted to dynamic environment changes, and integrated seamlessly with the perception and learning components of the project.



### Conclusion and Future Work

This project successfully integrated robotics, computer vision, and machine learning to create an adaptive block sorting system. The system's ability to learn and adapt to new sorting criteria, demonstrates potential for intelligent automation in industrial and research settings.

This project represents the first portion of my MSR final project. Future work could/will include:

1. Extending the system to handle a wider variety of objects with more complex shapes and materials, perhaps using an object detection model or vision-language-action model.
2. Implementing more advanced reinforcement learning algorithms to further improve adaptability and efficiency.
3. Exploring collaborative scenarios where the robot works alongside humans, incorporating safety features and intuitive interaction methods.
4. Integrating additional sensor data into the machine learning system.

By combining adaptive learning with precise robotic control, this project laid the groundwork for more intelligent and versatile automation systems. The potential applications span various industries, from manufacturing and logistics to healthcare and beyond.



<p class="text-center">
{% include elements/button.html link="https://github.com/scferro/franka_human_interaction" text="GitHub" %}
</p>

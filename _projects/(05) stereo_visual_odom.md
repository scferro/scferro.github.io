---
name: Realtime Stereo Visual Odometry
tools: [Python, OpenCV, Visual Odometry, Deep Learning]
image: https://scferro.github.io/assets/odom_main.gif
description: Used stereo images to track the position of the camera in real time. 
---

# Realtime Stereo Visual Odometry
<br>

### Overview
The primary goal of this project is to develop a real-time stereo visual odometry system that accurately estimates a robot's motion using a stereo camera. By analyzing changes in captured images over time, the system aims to provide precise localization data crucial for the autonomous navigation of robots. This project focuses on implementing a robust methodology, including image processing, feature detection, and trajectory estimation, with an emphasis on evaluating three feature detection methods: SIFT, ORB, and the deep learning-based SuperPoint.


### Video Demo
<iframe width="640" height="360" src="https://www.youtube.com/embed/hLsbvdzeaL0?si=GVzfOGvc_O5yF-40" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<br>


### Results
**KITTI Dataset Results:**

<img src="{{ site.url }}{{ site.baseurl }}/assets/kitti_traj.png"/>
<br>
1. **Camera Trajectory:** I plotted the estimated camera trajectories for each feature detection method (SIFT, ORB, and SuperPoint) alongside the ground truth from the KITTI dataset. This graph shows how accurately each method tracked the camera's movement over time. SIFT and SuperPoint trajectories closely followed the ground truth, while ORB showed some deviation, especially in turns.
<br>
<img src="{{ site.url }}{{ site.baseurl }}/assets/kitti_error.png"/>
<br>
2. **Trajectory Error:** I calculated the cumulative error for each method over the course of the trajectory. SIFT and SuperPoint had similar error profiles, both performing better than ORB. The error increased over time for all methods, which is typical for visual odometry due to drift.
<br>
<img src="{{ site.url }}{{ site.baseurl }}/assets/kitti_time.png"/>
<br>
3. **Frame Processing Times:** This graph shows the average time taken to process each frame for the different methods. ORB was significantly faster, processing frames in about a quarter of the time needed for SIFT. SuperPoint was about twice as fast as SIFT but still slower than ORB.

**RealSense Camera Results:**

<img src="{{ site.url }}{{ site.baseurl }}/assets/rs_traj.png"/>
<br>
1. **Camera Trajectory:** Similar to the KITTI results, I plotted the estimated trajectories for each method using data from the RealSense camera. In this real-world test, ORB performed surprisingly well, with a trajectory closer to the expected path than SIFT or SuperPoint.
<br>
<img src="{{ site.url }}{{ site.baseurl }}/assets/rs_fr.png"/>
<br>
2. **Frames Processed Per Second:** This graph shows the real-time performance of each method. ORB processed over 30 frames per second, significantly outperforming SIFT (7.5 fps) and SuperPoint (15 fps). This result emphasizes ORB's suitability for real-time applications.

These results show that while SIFT and SuperPoint were more accurate in controlled scenarios (KITTI dataset), ORB performed better in real-time, real-world conditions with the RealSense camera. This highlights the trade-off between accuracy and speed in visual odometry applications and demonstrates why testing with both datasets and real-world setups is crucial for a comprehensive evaluation.


### Procedure
1. **Image Acquisition:**
* Load left and right images from stereo camera setup
* Use KITTI dataset for development and testing
* Utilize Intel RealSense d435i camera for real-time implementation

2. **Image Preprocessing:**
* Apply histogram normalization to both images using cv2.equalizeHist
* Enhance contrast across the entire image
Improve feature detection in varying lighting conditions
* Ensure uniform visibility across the image


3. **Disparity Computation:**
* Calculate disparity map using cv2.Stereo_SGBM algorithm
* Semi-Global Block Matching for improved accuracy
* Compute pixel-wise differences between left and right images
* Extract depth information from stereo image pairs
* Create a smooth disparity map for accurate 3D reconstruction


4. **Feature Detection:**
* Implement and compare three methods:
* SIFT (Scale-Invariant Feature Transform)
* ORB (Oriented FAST and Rotated BRIEF)
* SuperPoint (deep learning-based technique)

5. **Feature Matching:**
* Use brute force matching techniques
* Apply cv2.NORM_L2 for SIFT and SuperPoint, which uses Euclidean distance for floating-point descriptors
* Apply cv2.NORM_HAMMING for ORB, which uses Hamming distance for binary descriptors
* Establish correspondences between features in stereo image pairs


6. **Match Filtering:**
* Use Lowe's ratio test to filter the best matches
* Compare distances of best and second-best matches
* Use a threshold (typically 0.75) to determine reliable matches
* Eliminate false correspondences
* Increase confidence in correctness of matched features


7. **3D Position Calculation:**
* Calculate 3D positions of matched points using disparity map and camera calibration data
* Apply triangulation to determine spatial coordinates
* Compute depth (Z) using focal length, baseline, and disparity
* Calculate X and Y coordinates in the camera frame


8. **Transform Estimation:**
* Estimate transformation between consecutive frames
* Use cv2.solvePnPRansac to calculate rotation and translation vectors
* Solve Perspective-n-Point (PnP) problem
* Employ RANSAC algorithm for robustness against outliers
* Convert 3D points of previous frame to 2D points of current frame


9. **Camera Position Update:**
* Update current camera position based on estimated transform
* Add new position to stored trajectory
* Maintain a continuous path of the camera's movement
* Repeat steps for each new frame to continuously track movement
* Update trajectory with each new frame processed



### Feature Detection Methods
The decision to test different feature detection methods in this stereo visual odometry project was driven by the need to balance accuracy, speed, and adaptability in real-time applications. The project evaluated three distinct feature detection methods: SIFT, ORB, and SuperPoint.

I compared these different methods was to determine which approach would be most suitable for real-time stereo visual odometry. SIFT was chosen for its robustness and ability to detect a large number of features, albeit at the cost of slower processing. ORB was selected as a faster alternative, potentially more suitable for real-time applications. SuperPoint was included to explore the capabilities of a modern, deep learning-based approach that could potentially be fine-tuned for specific use cases.

SIFT is a robust algorithm that's great at finding distinctive features in images that don't change much with scale, rotation, or lighting. It works by making a scale space representation of the image and finding key points that are stable across different scales. Then it calculates descriptors for these points based on local gradients. SIFT is super accurate and reliable, especially when there are big changes in viewpoint or lighting. It usually finds the most features of the three methods, which can lead to more accurate motion estimation. The downside is that it's computationally intensive, making it the slowest of the three and not great for real-time applications where speed is crucial. Features detected by SIFT are shown below.

<br>
<img src="{{ site.url }}{{ site.baseurl }}/assets/features_sift.png"/>
<br>

ORB is designed to be a faster alternative to SIFT. It combines the FAST keypoint detector with the BRIEF descriptor. FAST is used to find keypoints, and a modified version of BRIEF computes binary descriptors for these points. ORB is optimized for speed, making it way faster than SIFT and good for real-time use. It finds fewer features than SIFT but offers a nice balance of speed and performance. ORB works particularly well in situations where you need rapid processing, like real-time tracking or AR. It might be less accurate than SIFT when there are big changes in scale or rotation though. In my tests, ORB performed best overall with the RealSense camera in real-time, processing over 30 frames per second while still being reasonably accurate. Features detected by ORB are shown below.

<br>
<img src="{{ site.url }}{{ site.baseurl }}/assets/features_orb.png"/>
<br>

SuperPoint is a deep learning-based method developed by Magic Leap. It uses a fully convolutional neural network to detect keypoints and compute their descriptors at the same time. The network has a shared encoder and two decoder branches - one for finding keypoints and another for computing descriptors. SuperPoint is trained in a self-supervised way on a huge dataset, so it learns robust features that work well in lots of different environments. One of the coolest things about SuperPoint is that you can fine-tune it on specific datasets to make it work even better for particular tasks. In terms of accuracy, SuperPoint is often similar to SIFT, finding more features than ORB but fewer than SIFT. The trade-off is that it needs more computational power than ORB, so it's slower for real-time applications. In my project, SuperPoint showed potential but needed more optimization to be practical for real-time use - it was the second slowest and least accurate in the RealSense camera tests. Features detected by SuperPoint are shown below.

<br>
<img src="{{ site.url }}{{ site.baseurl }}/assets/features_superpoint.png"/>
<br>

Each of these methods has its own strengths and weaknesses when it comes to accuracy, speed, and adaptability. SIFT gives you high accuracy but it's slow, ORB processes things quickly with decent accuracy, and SuperPoint is a modern approach that can be tailored to specific applications but needs more computational resources.


### KITTI Dataset
For developing and testing my visual odometry algorithm, I used the KITTI dataset, a popular benchmark in computer vision and robotics research. It provides high-quality data from real-world driving scenarios, including urban, rural, and highway environments. KITTI's stereo image pairs were perfect for my stereo visual odometry work, capturing various driving conditions and lighting situations. This helped me test my algorithm's robustness. The dataset also includes ground truth data, which I used to calculate errors in my odometry estimates. I focused on the black and white stereo images and ground truth data, along with the provided intrinsic camera parameters needed for odometry calculations. KITTI allowed me to fine-tune my algorithm in a controlled way before moving to real-time testing with the RealSense camera.


### Intel Realsense
For real-time testing of my visual odometry system, I used an Intel RealSense d435i stereo camera. This camera was a great choice because it provides high-resolution black and white images from a pair of stereo infrared cameras, which work well in all lighting conditions. These images are similar to those in the greyscale KITTI dataset, making it easy to transition from development to real-world testing. To test the system, I mounted the RealSense on the back of a laptop and carried it through different environments, tracking its position and orientation in real-time. This setup allowed me to evaluate how well each feature detection method (SIFT, ORB, and SuperPoint) performed in practical, real-world scenarios. The RealSense's ability to capture stereo images at high frame rates was crucial for assessing the real-time capabilities of my algorithm, especially for the faster methods like ORB. This real-world testing phase was key in determining which method was most suitable for practical applications of stereo visual odometry.


<br>
<img src="{{ site.url }}{{ site.baseurl }}/assets/realsense_mount.JPEG"/>
<br>


### Conclusions and Future Work
The results of this project showed a clear trade-off between accuracy and speed among these methods. While SIFT and SuperPoint demonstrated higher accuracy in controlled scenarios, ORB emerged as the most practical option for real-time applications due to its speed and reasonable accuracy. This highlights the importance of balancing computational efficiency with precision in visual odometry systems, especially for real-time use cases.

**Future Work:**

* Test the system on low-power edge computing devices like Nvidia Jetson or Raspberry Pi to assess performance in resource-constrained environments
* Implement ORB and SIFT tracking in C++ to potentially improve processing speed
* Train the SuperPoint network on an application-specific dataset to enhance its performance for particular use cases
* Experiment with different resolutions on the RealSense camera to find an optimal balance between detail and processing speed

These improvements would make the system more robust, efficient, and applicable to various real-world scenarios.


<p class="text-center">
{% include elements/button.html link="https://github.com/scferro/stereo_visual_odom" text="GitHub" %}
</p>
# SFND 3D Object Tracking

Welcome to the final project of the camera course. By completing all the lessons, you now have a solid understanding of keypoint detectors, descriptors, and methods to match them between successive images. Also, you know how to detect objects in an image using the YOLO deep-learning framework. And finally, you know how to associate regions in a camera image with Lidar points in 3D space. Let's take a look at our program schematic to see what we already have accomplished and what's still missing.

<img src="images/course_code_structure.png" width="779" height="414" />

In this final project, you will implement the missing parts in the schematic. To do this, you will complete four major tasks: 
1. First, you will develop a way to match 3D objects over time by using keypoint correspondences. 
2. Second, you will compute the TTC based on Lidar measurements. 
3. You will then proceed to do the same using the camera, which requires to first associate keypoint matches to regions of interest and then to compute the TTC based on those matches. 
4. And lastly, you will conduct various tests with the framework. Your goal is to identify the most suitable detector/descriptor combination for TTC estimation and also to search for problems that can lead to faulty measurements by the camera or Lidar sensor. In the last course of this Nanodegree, you will learn about the Kalman filter, which is a great way to combine the two independent TTC measurements into an improved version which is much more reliable than a single sensor alone can be. But before we think about such things, let us focus on your final project in the camera course. 

## Algorithm Primary Steps

1. Image buffer has been used to store frames of data efficiently. Ring buffer implementation from boost library is utilized.
2. Camera frames are fed to YOLO object detection algorithm to find vehicle bounding boxes.
3. Lidar data has been cropped to include only ego lane or in other words; preceding vehicle.
4. Camera object boxes are associated with lidar data using shrink factor of 0.1.
5. Image keypoints are detected, descriptors are extracted and later they matched. Various detector, descriptor and matcher has been implemented and they can be called from strings for each.
6. Bounding boxes are associated in between frames. This is the first task of the project. Matrix data structure is used. Row of the matrix for previous frame and coloumn of the matrix for current frame are meant. Each match has been looped and matrix value for that row and coloumn has been increased. Highest value of row-coloumn pair has been selected from matrix as association of bounding boxes. Threshold of 30 matching has been used to avoid noisy matching.
7. TTC of lidar and camera is calculated and they filtered using median filter.

## Performance Evaluation

- Find examples where the TTC estimate of the Lidar sensor does not seem plausible. Describe your observations and provide a sound argumentation why you think this happened.

Even if preceding vehicle comes closer at frame of the lidar data calculated TTC value for lidar does not decreases all the time. Due to the object box size we assume laser points are associated with preceding vehicle this problem occurs. Even if median filter is used, outlier points shifts median value a bit. Outlier detection should be implemented as a next step. Standart deviation of TTC calculation from lidar points is around 2.5 in general.

- Run several detector / descriptor combinations and look at the differences in TTC estimation. Find out which methods perform best and also include several examples where camera-based TTC estimation is way off. As with Lidar, describe your observations again and also look into potential reasons.

TTC estimation for camera keypoints are directly depended on selected detector, descriptor and matcher combination. Even if there is couple of good choice for these combinations there are many bad combinations as well. For example HARRIS detector mostly detect less number of keypoint and this causes also less number of matches and as a result TTC estimation becomes not reliable. Having many keypoints and accurate matching in between frames is cruicial for acceptable TTC from camera.

In below best performing combinations are given. Complete list for each combination and their TTC values for both lidar and camera is given in a spreadsheet. Mean and standart deviation of values can be found in the file as well.

Detector Type | Descriptor Type | Matcher Type | Selector Type |
--|--|--|--|
FAST | BRIEF | MAT_BF | SEL_KNN |
--|--|--|--|
FAST | FREAK | MAT_FLANN | SEL_KNN |
--|--|--|--|
SHITOMASI | FREAK | MAT_BF | SEL_KNN |
--|--|--|--|

## Dependencies for Running Locally
* cmake >= 2.8
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux, Mac), 3.81 (Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* Git LFS
  * Weight files are handled using [LFS](https://git-lfs.github.com/)
* OpenCV >= 4.1
  * This must be compiled from source using the `-D OPENCV_ENABLE_NONFREE=ON` cmake flag for testing the SIFT and SURF detectors.
  * The OpenCV 4.1.0 source code can be found [here](https://github.com/opencv/opencv/tree/4.1.0)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory in the top level project directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./3D_object_tracking`.

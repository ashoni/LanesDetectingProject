**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./chess.png "Undistorted"
[image2]: ./undistorted.png "Road Transformed"
[image3]: ./binary.png "Binary Example"
[image4]: ./straight_bird.png "Warp Example"
[image5]: ./fit_lines.png "Fit Visual"
[image6]: ./result.png "Output"
[video1]: ./project_video_result.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

Code: IPython notebook located in "./P4.ipynb", cells 1-4. 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.

![alt text][image2]

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Code: IPython notebook located in "./P4.ipynb", cells 6-7 (functions abs_sobel_thresh, mag_thresh, dir_threshold, apply_thresholds). 

I used a combination of x, y, magnitude, direction Sobel operators and L and S components of HLS image.

![alt text][image3]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Code: IPython notebook located in "./P4.ipynb", cell 9 (function warp). 

I transitioned points based on these source and destination:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 234, 700      | 234, 720      | 
| 1074, 700     | 1074, 720     |
| 568, 470      | 234, 100      |
| 717, 470      | 1074, 100     |


I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Code: IPython notebook located in "./P4.ipynb", cell 12 (function find_lanes). 

I used peaks in a histogram method and a 2nd order polynomial:

![alt text][image5]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Code: IPython notebook located in "./P4.ipynb", cell 13 (function curvature).

Uses formula from http://www.intmath.com/applications-differentiation/8-radius-curvature.php 

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Code: IPython notebook located in "./P4.ipynb", cell 14 (function drawLine).

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_result.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

All in all, I followed the solutions described in the lessons without much additions. My first concern is curve radius: I'd like to find some way to check if it's correct or not, and how it's from the true value. My second concern are roads with many shadows, e.g. in test5 I couldn't get rid of a tree shadow. As a result, algorithm doesn't correctly distinguish line and shadow and detects line slightly wrong. At the current level it can be solved by averaging the lines from the previous pictures but I guess that more complicated shadows can cause more problems.


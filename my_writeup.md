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

[image1]: ./output_images/undistorted.jpg "Undistorted"
[image2]: ./output_images/warped.jpg "Road Transformed"
[image3]: ./output_images/sobel_hls_combined.jpg "Binary Example"
[image4]: ./output_images/histogram.jpg "histogram example"
[image5]: ./output_images/rectangle_line.png "find lane pixels"
[image6]: ./output_images/fit_polynomial.jpg "fit Visual"
[image7]: ./output_images/find_lane_line.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  
---

###Pipeline (single images)

### step1:Camera Calibration

There are 3 steps in this part:

#### 1.find corner points,create objpoints and imgpoints, i have used about twenty pictures in "./camera_cal/calibration*.jpg",using OpenCV function below:
    
ret, corners = cv2.findChessboardCorners(gray, (nx, ny), None)

#### 2.compute the camera calibration matrix and distortion coefficients,using opencv function below

ret, g_mtx, g_dist, rvecs, tvecs = cv2.calibrateCamera(objpoints,imgpoints,img_size,None,None)

### step2: Apply a distortion correction to raw image

apply this distortion correction to the test image using the `cv2.undistort()` function and obtained this result below: 

undist = cv2.undistort(img, mtx, dist, None, mtx)

![alt text] [image1]

### step3: Apply a perspective transform to rectify binary image ("birds-eye view").

in this part, i will do several steps;

#### first, i will delimit ROI that would be transformed in to a rectangle.

#### second, get transform matrix using OpenCV function below:

g_M = cv2.getPerspectiveTransform(np.float32(src_corners),np.float32(dst_corners))
g_M_inv = cv2.getPerspectiveTransform(np.float32(dst_corners),np.float32(src_corners))

#### then, apply perspective transform to raw image, the result shows below:


![alt text] [image2]


### step4: Find lane line
#### in this part, i will use color tranforms ,gradients,etc, to create a combined binary image

![alt text] [image3]

### step5: Detect lane pixels and fit to find the lane boundary  

#### 1.find the x option of lanes using histogram of binary image,the display effect follows:

![alt text] [image4]

#### 2. Implement Sliding Windows

find lane pixels using sliding windows, it shows below:

![alt text] [image5]

#### 3. fit a polynomial, it shows below

![alt text] [image6]


### step6: measure_curvature_real

#### fit polynormial:

$f(y)=Ay^2 + By + C$

#### measure curvature real

$R_{curve}=\frac{[1+(2Ay+B)^2]^{\frac{3}{2}}}{2|A|} $

### step7: draw lan line , and perspective back in to raw image, shows below:

![alt text] [image7]

### step8: at last, apply the project on video


the process video stored in "./output_videos/project_video_new.mp4"

---
## Discussion

Here I'll talk about the approach I took:

### 1. in step3: Apply a perspective transform to rectify binary image ("birds-eye view").

When doing a perspective transformation, I tried a lot of coordinate values for delimiting ROI(src_corners),
src_corners = [(603, 445), (677, 445), (1105, 720), (205, 720)]
dst_corners = [(205 + wrap_offset, 0), (1105 - wrap_offset, 0), (1105 - wrap_offset, 720), (205 + wrap_offset, 720)]

What are the principles to determine these coordinate values?

### 2.the project works well on "project_video.mp4", however can't works correctly on  "challenge_video.mp4" and "harder_challenge_video.mp4",

 it's caused by  "perspective transform"? how should i do to make it better?
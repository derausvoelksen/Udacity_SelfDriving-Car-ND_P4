## Writeup Template

---

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

[image1]: ./output_images/distortion_11.png "Distorted vs. Undistorted Camera Image"
[image2]: ./output_images/corners_unwarp2.png "Undistorted and warped Chessboard"
[image3]: ./output_images/undistorted.png "Undistorted and warped Test Camera Image"
[image4]: ./output_images/thresholded_binary.png "Multi Binary Thresholds"
[image5]: ./output_images/sobel_x.png "Sobel X Threshold"
[image6]: ./output_images/sobel_y.png "Sobel Y Threshold"
[image7]: ./output_images/gradient_magnitude.png "Magnitude of the Gradient"
[image8]: ./output_images/gradient_direction.png "Direction of the Gradient"
[image9]: ./output_images/color_thresholds.png "Color Thresholds"
[image10]: ./output_images/region_masked.png "Perspective Transformation"
[image11]: ./output_images/perspective_transform.png "Perspective Transformation"
[image12]: ./output_images/sliding_windows.png "Sliding Windows and polynomial Fit"
[image13]: ./output_images/shaded_lanes.png "Shaded Lane Marks"
[image14]: ./output_images/lane_mapping.png "Final Visualization of Detected Lane"


[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README
Ill briefly describe the outputs of each step of the pipeline created for the advanced lane detection. It strictly follows the above mentioned steps.

### Camera Calibration

The code for this step is contained in the first code cell (No. 9) of the IPython notebook located in "./advanced-lane-detection.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Undistotion of Image
This is the result of applying the cv.calibrateCamera() and cv2.undistort() function to the chessboard image:
![alt text][image2]

The same functions applied to the test image 6 looks like this:
![alt text][image3]

#### 2. Gradient Thresholds
Following along the lesson I added multiple theshold methods to the sample images. The final result looks like this:
![alt text][image4]

The "Subpipeline" can be found in Cell 22 and consists of the following:
* Sobel X (Cell 17)
![alt text][image5]

* Sobel Y (Cell 18)
![alt text][image6]

* Magnitude of the Gradient (Cell 19)
![alt text][image7]

* Direction of the Gradient (Cell 20)
![alt text][image8]

* Color Thresholds (Cell 21)
![alt text][image9]


#### 3. Masking and Perspective Transformation

The code for masking the binary image and the perspective transform is included in a function called `transform_perspective()`, which appears in cell 24 of the IPython notebook.  The `transform_perspective()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.
I chose the source points / mask as follows:
* upper left: width x 0.4, heigth x 0.65
* upper right: width x 0.6, heigth x 0.65
* lower right: width, heigth
* lower left: 0, height

The masking can be seen in the following image:
![alt text][image10]

The result of the transformation can be seen here:
![alt text][image11]


#### 4. Lane Detection

For detecting lanes I used the sliding window approach, and is implemented in cell 26. It also contains the polynomial fit of a polynomial of degree 2. The result looks like this:
![alt text][image12]

For a besser vizualization, the lanes markes are shaded:
![alt text][image13]

#### 5. Radius of Curvature and Vehicle Offset
The calculating of the curvature of the lanes is done is cell 28 (in pixels) and 29 (in meters). The Result is the following:

* Test Image 1
** Left Radius: 136.201481 meters
** Right Radius: 726.733892 meters

* Test Image 2
** Left Radius: 160.591974 meters
** Right Radius: 617.603350 meters

* Test Image 3
** Left Radius: 294971897.941627 meters
** Right Radius: 605.704369 meters

* Test Image 4
** Left Radius: 148.686924 meters
** Right Radius: 582.514646 meters

* Test Image 5
** Left Radius: 360171636.385536 meters
** Right Radius: 584.996524 meters

* Test Image 6
** Left Radius: 443373024.546571 meters
** Right Radius: 605.301870 meters

The calculation of the offset from the center lane is done in cell 37. The results are the following:
* Test Image: 1
** Offset: -0.165000 meters

* Test Image: 2
** Offset: -0.347500 meters

* Test Image: 3
** Offset: -0.242500 meters

* Test Image: 4
** Offset: -0.310000 meters

* Test Image: 5
** Offset: -0.117500 meters

* Test Image: 6
** Offset: -0.457500 meters

#### 6. Inverse Transformation and final Visualization
The vizualization of the detected lane is done in code cell 31 by applying the inverse perspective transformation:
![alt text][image14]

---

### Pipeline (video)

The Final Video can be found in the GitHub Repository:
https://github.com/derausvoelksen/Udacity_SelfDriving-Car-ND_P4/blob/master/P4_video_final.mp4


---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Implementing all the pipeline steps was very challenging, without the code snippets from the lessons I would not have made it.
The Pipeline is not very robust, as the challenge video results do not look too good (-> https://github.com/derausvoelksen/Udacity_SelfDriving-Car-ND_P4/blob/master/challenge_video.mp4)
The Project Video represented a very good-tempered situation, as almost no changes in brightness occure. This is where my pipeline fails in the more challenging video.
I assume that tweaking the threshhold parameters might lead to a more robust lane detection.
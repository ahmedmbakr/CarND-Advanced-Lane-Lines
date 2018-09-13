## Writeup Template

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

[img_calibration]: ./output_images/undistorted/calibration1.jpg "Undistorted"
[img_undistorted_road]: ./output_images/undistorted/test1.jpg "Road Transformed"
[img_color_thresh]: ./output_images/color_thresh/test1.jpg "Binary Example"
[img_top_down_prespective]: ./output_images/top_down_prespective/test1.jpg "Warp Example"
[img_fit_second_polynomial]: ./output_images/fit_poly_using_sliding_window/test2.jpg "Fit Visual"
[img_curvature]: ./output_images/curvature/straight_lines1.jpg "Radius of Curvature"
[img_marked_lane_lines_final]: ./output_images/final_marked_lanes_using_line_class/test1.jpg "Output"
[video_out]: ./output_videos/project_video.mp4 "a Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first three code cell of the IPython notebook located in "./advanced-lane-finding.ipynb" 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][img_calibration]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][img_undistorted_road]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at code cells 10).  Here's an example of my output for this step. 

![alt text][img_color_thresh]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `cal_undistort_unwarp()`, which appears in code block 8 in the notebook. I chose to hardcode the source and destination points in the following manner:

```python
src = np.float32([[520, 508], [770,508],[1030,687],[275,678]])
dst = np.float32([[275,508],[1030,508],[1030,678],[275,678]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 520, 508      | 275, 508        | 
| 770, 508      | 1030, 508      |
| 1030, 687     | 1030, 687      |
| 275, 678      | 275, 678        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][img_top_down_prespective]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][img_fit_second_polynomial]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in code block 23, and the resulting image is like this:

![alt_text][img_curvature]

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in code block 33 in the notebook.  Here is an example of my result on a test image:

![alt text][img_marked_lane_lines_final]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result][video_out]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Problems may occur if the lane lines are not clear in the camera image. I detect the false lines by checking the width between the two detected lane line points. I recover the false detected lines by averaging the points using 15 previous lane line pixels from previous video frames.
 

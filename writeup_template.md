---

I RECORDED THIS EXPLANATION VIDEO: 
https://photos.google.com/share/AF1QipOFYi7O_Rn9fRPNrudbn-A5M-hiyh2fwKv_TxFlLqtIf8RDmBv-wVny5rX1ko2aAA?key=MjhBUXg5QWJuSHJxcTh5VzNYdldlbUo1U19VRUR3

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

1. One time: Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
2. Apply a distortion correction to the raw input images.
3. Use color transforms, gradients, etc., to create a thresholded binary image.
4. Apply a perspective transform to rectify binary image ("birds-eye view").
5. Detect lane pixels and fit to find the lane boundary. (For initial polynomial, I used a column color histogram to determine inital x coordinates + margin for lanes candidates, following by sliding windows). Subsequent images use the initial polynomial as a prior to update the lane polynomial.
6. Determine the curvature of the lane and vehicle position with respect to center.
7. Warp the detected lane boundaries back onto the original image.
8. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)


[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"


[video1]: ./project_video.mp4 "Video"
[video1]: ./project_video_annotated.mp4 "# Define a class to receive the characteristics of each line detection
class Line():
    def __init__(self):
        # was the line detected in the last iteration?
        self.detected = False  
        # x values of the last n fits of the line
        self.recent_xfitted = [] 
        #average x values of the fitted line over the last n iterations
        self.bestx = None     
        #polynomial coefficients averaged over the last n iterations
        self.best_fit = None  
        #polynomial coefficients for the most recent fit
        self.current_fit = [np.array([False])]  
        #radius of curvature of the line in some units
        self.radius_of_curvature = None 
        #distance in meters of vehicle center from the line
        self.line_base_pos = None 
        #difference in fit coefficients between last and new fits
        self.diffs = np.array([0,0,0], dtype='float') 
        #x values for detected line pixels
        self.allx = None  
        #y values for detected line pixels
        self.ally = None Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points


---
###Writeup / README

###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook "main.ipynb" (or in lines # through # of the file called `some_file.py`).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

[image6]: ./output_images/undistored.jpg "Undistorted"


####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image in "main.ipynb". 
I do this with 
'combined_binary = color_and_gradient_threshold(undistorted)''


 Here's an example of my output for this step.  

[image6]: ./output_images/1.jpg "Left image"
[image6]: ./output_images/2.jpg "Left image"
[image6]: ./output_images/3.jpg "Left image"

![alt text][image3]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

I call the transform with
'binary_warped, M, Minv = unwarp(combined_binary)'

I chose the hardcode the source and destination points in the following manner:
src_upper_right = (703, 461)
src_lower_right = (1093, 715)
src_lower_left= (220, 715)
src_upper_left= (580,461)

and then my y dst points are 0 and np.max of y
and my x are off by 200 on the left and 300 on the right


[image6]: ./output_images/1.jpg "Middle image"
[image6]: ./output_images/2.jpg "Middle image"
[image6]: ./output_images/3.jpg "Middle image"


####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

out_img, out_img_red, left_fit, left_fitx, leftx, lefty, right_fit, right_fitx, rightx, righty, ploty= generate_polynomial(binary_warped)

Once I initialzed a first polynomial I just update it with 
out_img, out_img_red, left_fit, left_fitx, leftx, lefty, right_fit, right_fitx, rightx, righty, ploty = update_polynomial(binary_warped, left_fit, right_fit)

[image6]: ./output_images/1.jpg "Right image"
[image6]: ./output_images/2.jpg "Right image"
[image6]: ./output_images/3.jpg "Right image"


####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this with:     left_curverad, right_curverad, offset_from_lane_center = left_right_curverad(left_fit, leftx, lefty, right_fit, rightx, righty, ploty)

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Here is the example of my result on 3 test images:

[image6]: ./output_images/1.jpg "Right image"
[image6]: ./output_images/2.jpg "Right image"
[image6]: ./output_images/3.jpg "Right image"

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).


Here's a [link to my video result](./project_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Improvements for the future:
- improve the color and gradient thresholding. In particular filtering out 'black cracks' on the road surface (see the challenge video) could be optimized with this.
- Implement a Line class to include 'hysteris' or 'moving averages' of lines to don't have them wobble too much
Somethine like: class Line():
    def __init__(self):
        # was the line detected in the last iteration?
        self.detected = False  
        # x values of the last n fits of the line
        self.recent_xfitted = [] 
        #average x values of the fitted line over the last n iterations
        self.bestx = None     
        #polynomial coefficients averaged over the last n iterations
        self.best_fit = None  
        #polynomial coefficients for the most recent fit
        self.current_fit = [np.array([False])]  
        #radius of curvature of the line in some units
        self.radius_of_curvature = None 
        #distance in meters of vehicle center from the line
        self.line_base_pos = None 
        #difference in fit coefficients between last and new fits
        self.diffs = np.array([0,0,0], dtype='float') 
        #x values for detected line pixels
        self.allx = None  
        #y values for detected line pixels
        self.ally = None

- Build a Lane 'rejection' / outlier classifier to 'reject suggested lanes'. Rejections could be based on e.g. "distance between left and right lane must be between 3.5 and 4.5 m".





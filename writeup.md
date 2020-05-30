# Advanced Lane Finding Project

The goals / steps of this project are the following:

### 1 Camera calibration
*    Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.

### 2 Create Pipeline
*    Apply a distortion correction to raw images.
*    Use color transforms, gradients, etc., to create a thresholded binary image.
*    Apply a perspective transform to rectify binary image ("birds-eye view").
*    Detect lane pixels and fit to find the lane boundary.
*    Determine the curvature of the lane and vehicle position with respect to center.
*    Save frame data and create averages
*    Sanity check the results and correct frames if needed
*    Merge the detected lane boundaries, curvature and center offset back onto the original image.

### 3 Video Processing    
*    Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.
 
## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.
Camera calibration has done in the second code cell, in the camera_calirbration function. First, it generates a 9x6x1 array for object points. There are lots photos of a 9*6 chessboard pattern taken from different angles in the given path, the code reads all of the jpgs from there. These images must be converted from RGB to grayscale format with the OpenCV cvtColor function.
Then findChessboardCorners OpenCV function was used to get the 2D image points. Then the calibrateCamera OpenCV function was used to get the camera matrix and distortion coefficients by passing it the object point, and image point arrays. 

_note: Processing calibration1.jpg calibration4.jpg and calibration5.jpg were failed. There were missing corners on these images, I think._  

##### A Corrected chessboard image:

![Corrected image:](https://github.com/windmip/CarND-Advanced-Lane-Lines/blob/master/output_images/calibrated.jpg)

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image:

![Corrected image:](https://github.com/windmip/CarND-Advanced-Lane-Lines/blob/master/output_images/calibrated_test2.jpg)

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
In the 5th code cell, the create_warped_binary_image function contains these transforms. First, the image was converted to HSV color space. Then three binary images were made, each of them contains a filter:
* Filter 1 (displayed with red color) makes the x oriented derivations using the Sobel operator. The input is V(value) value of HSV values on each pixel. Value is something like brightness, so the result shows the x oriented gradients. Threshold parameters were given in the function header (32,255)
* Filter 2 (displayed with green color) is based on saturation value, and 
* Filter 3 (displayed with blue color) is based on hue. The bounds were 235,250 and 20,40 repeatedly. I found the combination of this one, and previous one useful finding the yellow line in shadows

Raw data of filter 2 and 3 could be used directly, filter 1 had to be converted to unsigned byte from float first. Here is a  stacked RGB example of it: 

![Corrected image:](https://github.com/windmip/CarND-Advanced-Lane-Lines/blob/master/output_images/test2_warped.jpg)

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.
It is the 6th code cell. The OpenCV function GetPerspectiveTransform was used two times, to get the perspective transform matrix and get the invert matrix too. Source points were chosen manually selecting two close and two distant points of each line.

_note: I had to go back here from curvature calculation to fine tune curvature result with slightly modifying ytop1 and ytop2 here._

<pre>
Souce coordinates: 
top1    [ 100, 719]  top2    [ 553, 450] 
bottom1 [ 737, 450]  bottom2 [1200, 719]

Destination coordinates: 
top3    [100,719]    top4    [100, 100] 
bottom3 [1210, 100]  bottom4 [1200,719]
</pre>

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?
There are two functions supporting this functionality: __polyfit_sliding_window__ and __find_lane_pixels__.
__find_lane_pixels__ is used to determine pixels of a line. It is based on sliding window algorithm. First the algorythm finds the start of the line by creating a historgram (#8) of the sum of the active pixels projected to the x axis from the bottom half of the  screen. Luckily perspective transform cuts off the unrelevant noise from the image so the bottom half of the image usually contains useful shards of vertical lines that form the traffic lines IRL. So the first two bound boxes are positioned to the start of the line. (#17 #20) An loop iterates over the seven sliding windows (#51) (I found that seven window handles misaligning better than nine) After the coordinates are calculated (#53), the active pixels were collected from here (#64), then the x coordinate of next sliding window above was averaged (#74) from the x position of the collected pixels. After the iteration is finished, all of the line pixel passed back to __polyfit_sliding_window__

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.


#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.


### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

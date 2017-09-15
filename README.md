# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./examples/lane_detection_pipeline.JPG
[image3]: ./examples/houg_transform1.JPG

This project attempts to detect and annotate lane lines on the road from a Video Stream

![alt text][image1]


---

### 1. Lane Detection Pipe Line

![alt text][image2]


My pipeline consisted of below steps:

1. Capture each frame of the Video stream and convert the image to gray scale
2. Apply Gaussian filter to smoothen the image
3. Use canny edge detection technique to detect the edges of the image
4. Assuming that the camera position and angle is fixed, region of interest in which we can find the lane lines can be computed.
5. Mask the image such that only pixels in the region of interest are non-zero.
6. On the mask image which also contains the edges, transform the image to contain only lines using Hough Transform. 
   Hough transform helps in detecting lines from the images. Hough transform is explained in detail in section below
7. Having detected the line segments using Hough transform, join the line segments by fitting line through the points and extend the 
   lines based on the slope of the fitted line. This step is explained in section below.

### Hough Transform

Typically an image is plotted as x vs y in image space. In Hough space, an image is plotted as m vs b, where m is slope and b is intercept. 
Thus, two lines with same slope but different intercepts may appear as below:

![alt text][image3]

https://en.wikipedia.org/wiki/Hough_transform

Using the idea that points on the same line share the same slope, image space can be transformed to Hough space 
and detect the lines within the image. This line detected image is processed to detect the precise lane lines 
as described in next section.

### Draw lines to detect lanes

Below are the steps followed to detect the lane lines:

1. This method takes the detected lines as input. Each input line is represented by two end points (x1, y1) and (x2, y2)
2. For each line, check if the end points (x1, y1) and (x2, y2) are within the region of interest
3. Compute the slope of the line as:
   slope = (y2 - y1) / (x2 - x1)
4. If the slope is negative, this line could be a left line. If slope is positive, this line could a right line.
5. Add the points to negative slope list or positve slope list depending on slope is positive or negative.
6. Fit a straight line on the list of positive slope points to form right lane line as below: 

	np.polyfit(pos_x, pos_y, 1)	

7. Fit a straight line on the list of negative slope points to form left lane line as below: 

	np.polyfit(neg_x, neg_y, 1)

8. Based on the fitted lane lines, draw left and right lane lines
9. It is possible when lines are detected, lane lines are dotted, so full extent of lines may not be there in certain frames.
10. In order to extend the full lane lines we need to do the following:

    a) Left bottom extension
    b) Left top extension
    c) Right bottom extension
    d) Right top extension

    General idea for doing the extension is that we already know the coordinates of the end points and the slope of the line. 
    We also know the boundaries of the region of interest. Thus, we know y-coordinate of the desired end point. Using equation 
    of straight line, we can compute the x-coordinate of the desired end point as shown below:

    Equation of line is y = m * x + c

    If we know y and m, we can compute x as:

     x = (y - c) / m, where m is the slope of the line and c is the x-interecpt
	
### 2. Shortcomings with the current pipeline


    Drawbacks of the current pipeline is that, it assumes that camera position and camera angle is fixed. It also assumes that 
    driveway is straight. However, if we have curved lane lines or path, lane detection may fail. Also, we are assuming that the  
    lane lines are the only possible lines within the region of interest. But we may find different kinds of objects in the drive
    way that might violate this assumption. Also, this lane detection technique needs to be tested for varying lighting conditions.

### 3. Possible improvements to the pipeline

    I have assumed fixed camera position and angle in building this pipeline. This solution can be made more robust by considering
    camera angle and position. Using camera angle and position, accurate region of interest could be computed. Also, slope of the
    lines may not vary a great deal from frame to frame. Thus possibly we could compute the average slope for the past few frames
    to stabilize the lane lines.

    Another possible approach could be to use Kalman filters. We can compute the velocity of the vehicle and use that information
    to predict the position of the lane line in next frame. This would help avoid having to detect the lane lines in each
    and every frame.

    Yet another idea is to use Deep learning. We can collect the annotated videos which might use standard computer vision
    techniques discussed above to build a train data set, then use a classifier built using Convolutional Neural Networks (CNN) to detect
    the lane lines. Advantage with deep learning is that we need not perform any transformation or feature engineering on the image as 
    CNN can take raw pixels as input and predict the coordinates of the points on the lane lines.

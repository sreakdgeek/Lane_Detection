# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./examples/lane_detection_pipeline.JPG

This project attempts to detect and annotate lane lines on the road from a Video Stream

![alt text][image1]


---

### Reflection

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

### Draw lines to detect lanes


### 2. Shortcomings with your current pipeline

One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Possible improvements to the pipeline

A possible improvement would be to 

Another potential improvement could be to 

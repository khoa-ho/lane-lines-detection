# **Finding Lane Lines on the Road** 

## Writeup

---

**Finding Lane Lines on the Road**

When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

This project involves detectecting lane lines in images using Python and OpenCV.


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Image processing pipeline

My pipeline consisted of 7 steps. First, I filtered in white-ish and yellow-ish pixels and turned everything else black. Then, to facilitate Canny edge detection, I converted the image to grayscale and applied Gaussian smoothing. The next step is Canny edge detection. To further remove unwanted artifacts, I applied a positive mask on the region of interest (ROI) while keeping everything outside black. After that, I used Hough transform to draw a lines representing each lane. The final step is to combine that line image with the original image. 

To draw a single line on the left and right lanes, I modified the draw_lines() function by adding the following steps. First, I calculated the slope for each lines and filtered in lines with a reasonalble value of slope. The upper and lower threshold were obtained through visual inspection and trials-and-errors. Then I classified whether a given line is on the left lane, right lane, or could not be on any lane based on its slope and the x-coordinates of its two endpoints. Next, I used linear regression to obtain a best-fit line going thourgh the endpoints of all the lines on each lane. Now, each lane is represented by a single line, going from the bottom the image to the top of the ROI.


### 2. Potential shortcomings with the current pipeline


My current approach will not perform if the road's color is too similar to the lanes' color (due to the road's inherent surface color or to the lighting conditions), since it only uses information from each frame instead of taking into account the continuity of the lane throughout several frames. Furthermore, I hard-coded several parameters like the location (and area) of the ROI, the RGB thresholds, the slope thresholds, and a host of other tuning parameters for the Canny edge detection and the Hough transform after extensive testing on specific videos. This current pipeline is unlikely to work as robustly for a very different video.


### 3. Possible improvements

One way to improve this pipeline is to take into account previous frames with frames more immediate in the past have higher influence on the current frame than those further back. Maybe during the linear regression step in the draw_lines() function, I could calculate a weighted average of the slope (and the intercept) based on the best-fit line of the current frame and those of several previous frames. The weight can be exponentially or linearlly decaying further back into the past (My intuition goes with the former.) 

# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteCurve.jpg "solid white curve after pipeline"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline is in the **find_lane(img)** method and consists 6 steps:

Firstly I covert the image to grayscale with the **grayscale(img)** method

I then apply gaussian smoothing to that image with a kernel size of 5 using the **gaussian_blur(img,kernel_size):**

I then run the canny algorithm on the output with a low threshhold of 50 and a high threshold of 150 using the **canny(img, low_threshold, high_threshold)** method

I then apply a regional mask where I remove everything from the image other then a triangle with a vertex at each of the bottom corners of the picture and a vertex at the center of the x axis and 0.58 from the bottom of the image(y coordinate). I do so using the **region_of_interest(img, vertices)** method.

I then apply the a hough lines transform with rho of 2, theta of pi/180, a threshold of 15, a minimum line length of 40, and a maximum line gap of 20 using the **hough_lines(img, rho, theta, threshold, min_line_len, max_line_gap)** . In that method the **draw_lines(img, lines, color=[255, 0, 0], thickness=5)** method is called with default parameters but i modified it. For all of the lines composing the left lane and all of the lines composing the right lane (separated based on both gradient and the locations of the ends of the line segment) I added the end points to a set. I then performed linear regression on each set of points to find a line of best fit. I use this line to draw the line segment representing each lane from the bottom to the middle of the image.

The final step in my pipeline is to call the **weighted_image(img, initial_img, α=0.8, β=1., γ=0.)** method to overlay the lines from the hough transform onto a copy of the original image. I return that output.

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline

One shortcoming would occur in a situation with more than one lane in the visual region (such as a bikelane) at which point the lines would not be able to be distinguished.

Also, if the street curves ahead, the algorithm assumes all lines will be straight which is incorrect. 

Also, in certain weather conditions (such as fog or rain) the canny algorithm may record too much noise to distinguish lane lines.

Finally, if the street is going either up a hill or down a hill in such a way that the lane lines are outside the region visible with the mask, the lanes will not be seen at that point

### 3. Suggest possible improvements to your pipeline

One possible improvement would be to perform a form of non_linear best fit in **draw_lines** in order to catch curves ahead.

Another possible improvement is to have different canny parameters depending on driving conditions.

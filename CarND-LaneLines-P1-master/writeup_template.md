# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale_example_function.jpg "Grayscale"
[image2]: ./examples/gaussian_example_function.jpg "Gaussian Smoothing"
[image3]: ./examples/canny_example_function.jpg "Canny Edge Detection"
[image4]: ./examples/canny_example_function.jpg "Canny Edge Detection"
[image5]: ./examples/lines_example_function.jpg "Line Segments"
[image6]: ./examples/weightedLines_example_function.jpg "Weighted image with lines"
[video1]: ./test_videos_output/solidYellowLeft.mp4 "Solid Yellow Left Video with lines"


---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline has the following steps as follows:

**Grayscale:

The image is converted to one color channel.

![alt text][image1]

**Gaussian Smoothing:

Applying a Gaussian blur has the effect of reducing the noise in the image; a Gaussian blur is like a low pass filter. This helps in the edge detection.

![alt text][image2]

**Canny Edge Detection:

This type of edge detection helps in identifying the structural information of objects in the image.

The Process of Canny edge detection algorithm can be broken down to 5 different steps:
*Apply Gaussian filter to smooth the image in order to remove the noise
*Find the intensity gradients of the image
*Apply non-maximum suppression to get rid of spurious response to edge detection
*Apply double threshold to determine potential edges
*Track edge by hysteresis: Finalize the detection of edges by suppressing all the other edges that are weak and not connected to strong edges.

![alt text][image3]

**Region of Interest (ROI) masking:

I take advantage of the fact that the camera is front fascing and mounted fixed on the vehicle. I chose a quadilateral on to mark the rough regions where I want the focus of detecting edges to be, all the other unwanted noise like the background is discarded.. The 4 points are as follows:

left bottom = (50,540)
right_bottom = (960,540)
right_top = (530, 540 * 0.6)
left_top = (435, 540 * 0.6)

![alt text][image4]

**Hough Transform:

Partameters: 
    rho = 1 # distance resolution in pixels
    theta = (np.pi/180) # angular resolution in radians
    threshold = 40     # minimum number of votes 
    min_line_len = 50 # minimum number of pixels making up a line
    max_line_gap = 150    # maximum number of pixels between line segments

For images:
draw_lines() function has been modified to draw single line on the left and the right lane by fitting the 1d line x=Ay+B and finding out the x values of the bottom and top for the left lane and right lane as we know the y_top and the y_bottom which are fixed from our versices selected from the ROI. The function is constructed in such a way that if no lane lines are detected from the hough lines transform we will draw the lines from the previous frame.

For video:
To draw_lines() function for the images I added a small change for the first frame I took the average of the slopes and the x points and the y points and calculated the y-intercept and reverse engineered the values of the x's from the formula (y-c)/m where y is the y_top or y_bottom and m is the average slope. Later I used the draw lines function that I used above.

![alt text][image5]

![alt text][video1]

**Weighted image:

I lay the lines computed above on the original image inorder to display the original image with lines.



### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when shadows creep into the image which could be detected as lines.

Another one would be that I feel by converting to grayscale we are loosing some of the information, as in the yellow lines can be detected better if we use Red channel or any other channel.


### 3. Suggest possible improvements to your pipeline

Possibly we could use something like a linear regression to fit a line on the opints calculated or use some deep learning to identify lane lines. 

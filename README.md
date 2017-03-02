#**Finding Lane Lines on the Road** 

---

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[pipeline1]: ./pipeline_images/step1_gray.jpg "Grayscale pipeline"
[pipeline2]: ./pipeline_images/step2_gaussian.jpg "Gaussian pipeline"
[pipeline3]: ./pipeline_images/step3_canny.jpg "Canny pipeline"
[pipeline4]: ./pipeline_images/step4_roi.jpg "ROI pipeline"
[pipeline5a]: ./pipeline_images/step5a_hough_original.jpg "Hough original pipeline"
[pipeline5b]: ./pipeline_images/step5b_hough_straight_lines.jpg "Hough straight lines pipeline"
[pipeline6a]: ./pipeline_images/step6a_combined.jpg "Combined a pipeline"
[pipeline6b]: ./pipeline_images/step6b_combined.jpg "Combined b pipeline"
[annotated0]: ./anotated_images/annotated0.jpg "annotated"
[annotated1]: ./anotated_images/annotated1.jpg "annotated"
[annotated2]: ./anotated_images/annotated2.jpg "annotated"
[annotated3]: ./anotated_images/annotated3.jpg "annotated"
[annotated4]: ./anotated_images/annotated4.jpg "annotated"
[annotated5]: ./anotated_images/annotated5.jpg "annotated"
---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 6 steps:

**Step 1: Turn image into grayscale channel**

![alt text][pipeline1]

**Step 2: Apply Gaussian blur smoothing**

I use kernel size of 5, which provides a soft smoothing

![alt text][pipeline2]

**Step 3: Apply Canny edge detection**

I use low threshold of 70 and high treshold of 150. To be honest these numbers I got it by trial and error, and the optimization was done by manually eyeballing the image result

![alt text][pipeline3]

**Step 4: Apply region of interest (ROI)**

Here I use the region of interest to basically filter out everything except the region where we expect the two lanes will be. This method does have its limitation; it will work on the test images & videos in this project, but there are a lot other scenarios where it will fail to properly isolate the correct lane lines area. I will elaborate more in section 2 "potential shortcomings"

![alt text][pipeline4]

**Step 5: Apply Hough line** 

Then I apply Hough lines with the following parameters:
    rho          = 2
    theta        = np.pi/180
    threshold    = 20
    min_line_len = 10
    max_line_gap = 6
    
My pipeline allows two different line drawing options at this stage:

a) Draw lines according to the lane markers - smultiple short & long markers

This path execute the original algorithm of the draw_lines() function, no additional change was done

![alt text][pipeline5a]

b) Draw a single line on the left and right lanes

![alt text][pipeline5b]

In order to get the long lines that are smooth a few processing steps were done:

1. First I sum up the x1, x2, y1, & y2 points of all the left lines and then do the same for the right lines. Note: I dentify the left lines by finding lines whose slopes are less than -0.4 and the right lines by taking lines whose slopes are more than 0.4. This way I also filter out the outliers with almost horizontal slope.
2. Then I divide the summed x1,x2,y1,y2 points by total number of lines (do for boh right & left lines) to get the averaged points.
3. From the averaged points of the left & right line, I create a line equation for each one of them by following the below math:

  Line equation: 
  y = slope*x + intercept

  slope = (averaged_y1-averaged_y2)/(averaged_x1-averaged_x2)</br>
  intercept = averaged_y1 - slope*averaged_x1

4. With the line equation I am able to draw a line whose y endpoints are on one end at the bottom of image (539) and on the other end at top of region of interest (317). I find the x endpoints - of both right & left line - by plugging in the y endpoints into the equation:

  x = (y - intercept)/slope
  
  i.e.</br>
  x_bottom = (540 - intercept)/slope</br> 
  x_top = (317 - intercept)/slope 317

  Now I get the endpoints to draw the extrapolted single line for both right & left lane

5. Finally I draw red lines with thickness of 6 for both lines and apply the weighted_img function on them to get the soft overlay effect.


**Step 6: Combine input image with final line image**

This is simply just the weighted_img of the original image & the hough line image from step5.


![alt text][pipeline6a] ![alt text][pipeline6b]

Here are the results of my pipeline drawing extrapolated & averaged left & right lane markers for all the 5 test images:

![alt text][annotated0] ![alt text][annotated1] ![alt text][annotated2] ![alt text][annotated3] ![alt text][annotated4] ![alt text][annotated5]

###2. Identify potential shortcomings with your current pipeline

In my opinion there are two major shortcomings in my current pipeline:

1. Fixed region of interest (ROI) mask: This method works for the sample images & videos (apart from the challenge) as the roads are all within the fixed region of interest. However in real world situation, even if the camera position is fixed, **the road can go uphill or downhill, car changing lane, and all kinds of other corner cases where the region of interest for the lanes will go out of my ROI bound. When that happens the lane detection will breakdown.**

2. Lane lines have to be relatively straight: with the way my canny edge and hough transform is configured, **if the lane is a sharp corner, it won't be able to identify properly the lane lines, especially the degree of the turn. So the self-driving car has high probability of missing a sharp corner (and drive into the abyss)!**


###3. Suggest possible improvements to your pipeline

1. Replace the static region of interest mask with a convolution neural network that does dynamic image segmentation to find the region of interest of the lanes. With an image segmentation convnet, the ROI will be dynamic and changing in real-time, thus it can still find the lanes region of interest in all kinds of corner cases, where the road goes steep uphill, downhil, changing lane, sharp corners, etc (providing the convnet has high accuracy).

2. For the Hough Transform, we need something that can detect more than just a pretty straight line as the lanes can turn sharply. I think there are computer vision techniques out there, where you can hand-craft more complex features, so we can also detect bended lanes.



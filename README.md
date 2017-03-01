#**Finding Lane Lines on the Road** 

##Writeup Template

###You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

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
[annotated0]: ./annotated_images/annotated0.jpg "annotated"
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

1. First I sum up the x1, x2, y1, & y2 points of all the left lines. Do the same for the right lines.
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

![alt text][annotated0]

###2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


###3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...

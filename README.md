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
---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps:

**Step 1: Turn image into grayscale channel**

![alt text][pipeline1]

**Step 2: Apply Gaussian blur smoothing**

![alt text][pipeline2]

Step 3: Apply Canny edge detection

Step 4: Apply region of interest (ROI)

Step 5: Apply Hough line 

My pipeline allows two different line drawing options at this stage:

i) Draw lines according to the lane markers - short, long, single, or multiple

This path execute the original algorithm of the draw_lines() function, no additional change required

ii) Draw a single line on the left and right lanes

SFirst, I converted the images to grayscale, then I .... 

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


###2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


###3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...

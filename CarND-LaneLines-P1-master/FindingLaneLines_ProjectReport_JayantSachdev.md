# **Finding Lane Lines on the Road** 

[//]: # (Image References)

[mask1]: ./test_images_output/Masked1.png 
[mask2]: ./test_images_output/Masked_adjusted.png 
[mask3]: ./test_images_output/Masked_adjustedforpicture4.png 
[starting]: ./test_images_output/solidWhiteCurve_computed1.jpg 
[Image1_edited]: ./test_images_output/solidWhiteCurve_computed6.jpg 
[Image2]: ./test_images_output/solidWhiteRight_computed1.jpg 
[Image3]: ./test_images_output/solidYellowCurve_final.png
[Image4]: ./test_images_output/solidYellowCurve2_final.png
[Image5]: ./test_images_output/solidYellowLeft_final.png
[Image6]: ./test_images_output/whiteCarLaneSwitch_final.png
[Image7]: ./test_images_output/solidWhiteRight_computed_final.jpg


---
### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

For the first part of the project, we had to draw lines to show the left and right lanes for each of the 6 sample images. 
My pipeline consisted of 15 steps to determine the lines and draw it on the each image as desired:

1. Load in the images
2. Convert to grayscale
3. Blur the image
4. Define low and high thresholds (defaults at 50,150 respectively)
5. Apply canny edge detection
6. Define vertices for masking, use default from quiz. 
7. Mask image
	a. Print out masked image
	b. Adjust vertices till it seems reasonable
8. Define parameters for hough transform
9. Generate image with hough transform
10. Adjust canny thresholds and hough parameters till the image looks desirable
11. Load in next image
12. Refine mask
13. Refine parameters
14. Check on previous images
15. Repeat till you go through all images

Steps 1 through 6 were fairly straightforward with the implementation mimicking the pipeline used in the vision system fundamental lectures. One modification i made was to define the bottom left and right vertices and the apex left and right verticies, rather than using the corners of the images for the bottom points, as shown in the lectures. This allows more flexibility to restrict the desired area. 

Starting with the default verticies used in the lectures, i noticed that a lot of undesired features were shown in the image and the mask could be improved:

![alt_text][mask1]

I played with the verticies until the mask appeared to be more effective:

![alt_text][mask2]

I then proceeded to use the hough transform to generate the lines on the first image. I used the default parameters from the hough transform quiz as a starting point:

![alt_text][starting]

I then adjusted the parameters for the canny edge detection thresholds and the hough transform until i was satisfyied with the image:

![alt_text][Image1_edited]

I went through and verified how it looked on image 2:

![alt_text][Image2]

I then made some adjustments for image 3 so it looked acceptable:

![alt_text][Image3]

and went back to make sure the algorithm still was acceptable on previous images. This process was repeated for the last 3 images, except for image 4, the mask was also adjusted. Final lines on image 4:

![alt_text][Image4]

Final lines on image 5:

![alt_text][Image5]

Final lines on image 6:

![alt_text][Image6]

The next process was checking to see if it ran on the video stream and it did. So i moved on to modifying the drawlines function to draw a single line. To separate them, i used the slope. I then printed all the slopes and determined that right lines had positive slope (due to matplotlib processing the lines) with an average value of 0.6. I then created an array of slopes and intercepts for both lines. I only added slopes and intercepts that fell within points with slope of 0.6 +/- 0.15 for the right and -0.75 +/- 0.15 for the left. I then took the average slope and intercept value to draw the line from. In order to draw the line, i initially tried setting the 1st point at the bottom of the image where the lanes begin, however i had issues with this. As such, i moved the starting line point before the start of the image and this worked well. For the 2nd point, i chose a y2 value that was around the point i selected for the mask. the reasoning was that if i am satisfied with the data in the mask, i would be satisfied with length of the lines going till that point. I solved for x1 and x2 using the y = mx + b equation. I then drew the line using the points and i increased the thickness to 10 so that it appears thicker like in the video. The result on the final image can be seen below:

![alt_text][Image7]

One issue i had to overcome when implementing this in the video stream was 1/2 instances where there were no lane lines detected. This gave a 'NaN' error. I overcame this by setting the slope and intercept values to 0 when this occurs so we do not add a line to the image in these scenarios. 

### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming with my potential pipeline is that i do not filter the lane lines with respect to the intercept value. Doing so will help cut out the noise. 

Also, my current pipeline does not hold onto the last known good lane line for when a frame in a video results in an image with no lane on 1 side. I can either improve the parameters, which may introduce noise and unwanted lines or i can sample and hold the lane line for a certain amount of time. 

Another issue is that the current method does not take into consideration the curvature of the lane lines. The lane line would be better approximated by a 2nd or 3rd order polynomial and in the final video we can see that the linear approximation does not adequately model the lane lines. 



### 3. Suggest possible improvements to your pipeline

To improve my pipeline, i would filter right and left lanes by their intercepts to improve on the line selection in challenging area's as seen in the challenge video. 

Also, i would implement a sample and hold strategy with a turn off delay to overcome 1/2 frames where no lines are detected and improve continuity of the algorithm. 

Finally i would model the lane lines as 3rd order polynomials in order to better approximate the lanes. This would allow us to properly detect and draw lane lines in the challenge video.  

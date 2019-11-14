# **Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of around 7 simple steps. 
First, I converted the images to grayscale, then I applied Gaussian smoothing, then Canny edge detection to the image, proceeding to create a mask on the image to define a working region. Once the region was defined, I applied a Hough transform on the selected region and manipulated the lines to get two distinct lane lines. Once the lines were distinct, these in turn were drawn on top of the original image.

These steps were all achieved using OpenCV techniques laid out in the lessons preceding this project.

When doing the simple white and yellow lines in the first part of the project, I opted not to use color masking to simplify the work that I was doing.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by hardcoding a quadilateral based on the images received as an input. This was not ideal for many reasons, but worked for the more simple videos. 

With the lines that came in the from Hough tranform, I combined all of the lines into a Pandas dataframe and then split them based on angle thresholds that were fine tuned to isolate the lines that were relevant to the occupied lane.

After slpitting the data into left and right I fit a line through each of these sets and got an equation for each proposed lane line, and plotted.

This solution worked alright for the first two videos, but was horrible for the challenge video, and thus needed to be fixed.

To tackle the challenge video, I replaced the mask with a series of contraints on how far the Hough lines are expected to be from the middle of the car hood and introduced a new contraint on the expected angles of these lines.

As there was much more contrast in the challenge video, I introduced color masking by first converting the images from RGB to HSV to allow a better distinction between the colors in several light conditions. The color masking when combined with the distance and angle constraints for the Hough lines allowed for enough to identify lines of interest.

The last problem was that the lines seen from frame to frame could still be seen as drastically different, so I used the assumption that the previous lane line would be a good approximation for the next lane line if no line could be found of if the line was drastically different.

I decided to also add in a smoothing parameter that would only allow lines to change by 20% per iteration.

The results of this can be seen in the jupyter notebook test_video_results file.


### 2. Identify potential shortcomings with your current pipeline


I believe that my pipeline benefits greatly from the fact that all of the videos and test images are in the daylight. With the assumptions made when lane lines are not found, this would likely almost certainly fail in low light conditions, or in inclement weather. The contrast of lane lines reverses in rainy weather, would would also greatly affect the usefulness of this pipeline.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to factor in the average contrast the image changes. For instance, when shade falls across the road, all parts of the road covered in shade will be affected by a similar factor of darkness

Another potential improvement could be to factor in the relative position of other vehicles and their predicted yaw rates in order to inform the likely path of the lane that your vehicle occupies.
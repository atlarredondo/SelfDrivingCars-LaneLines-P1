# **Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./shortcoming.png 

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 8 steps:
1. Converted the images to grayscale, `grayscale`.
2. Applied gaussian blur filter to the image, `gaussian_blur`.
3. Get Canny transform of the image, `canny`.
4. Masked the image with our specific region of interest `region_of_interest`.
5. Get the hough lines by using the HoughLinesP function, `hough_lines`.
6. Average the lines to get a final left and right line using polyfit,`draw_driving_lines`.
7. Used the line parameters to obtain 2 points per lane and draw a line using
the coordinates as parameters on a masked image, `get_avg_final_points`.
8. Combined the original image with the masked image, `weighted_img`.

Steps 5, 6 and 7 are all executed on the hough_lines method.

The first issue with the pipeline was dealing with the spots in the image that might contaminate the line edges that we care about. On the `solidWhiteRight.jpg` test image there is a spot right between the lines and a bit of glare, that caused some issues when we try to calculate the lines. These spots on the image have similar properties as the lanes and I worked on trying to remove them on the preprocessing stage of the pipeline(Steps 1 and 2), by increasing the kernel size used `gaussian_blur`. This change in parameters made the noisy spots almost disappear with the content of the image around them.

The second issue was focused on getting the most information from the `canny` and `HoughLinesP` methods. This was done by tweaking their parameters until I got a desirable outcome. I mostly focused on the `min_line_length` and `max_line_gap` parameters since I found changes on their values affected the results significantly. I ended choosing 5 and 15 as the values for these two parameters.

My third issue came when I tested on the video. I noticed that only the spots that looked like a lane had been marked by the `draw_lines` method. This did not look acceptable and I tried to calculate a final average line by using the lines coordinates from the `HoughLinesP` method. I used the `polyfit`  to calculate a new line that can pass through all the lines on the right and left side of the image.
Since, np.polyfit returns the m(slope) and b(y offset) parameters, four new points were computed in order to find the coordinates needed to draw the lines using cv2.line function. 

My final issue came from the noise introduced by creating the final average line. When I tested this version of the pipeline, I observed that the lines were shaking quite a bit and it was noticeable that the parameters for the final lines were slightly different on every frame. To fix this I moved all of the helper function into a class and introduced an array to keep track of the latest line parameters. I used this array to calculate the mean of the line parameters instead of the average line parameters. This reduced most of the noise. 
This method was modified to include the draw_driving_lines which computes the average of the lines returned from the cv2.HoughLinesP function.



### 2. Identify potential shortcomings with your current pipeline

On the tested video there are huge changes on the lanes parameters at several times. I believe this change is due to some frames that do not work well with the preprocessing method parameters. (Fix discussed in the improvements section)

![alt text][image1]

Also, this pipeline completely fails the optional challenge and I which I had more time in order to deal with this edge case.


### 3. Suggest possible improvements to your pipeline

For the first shortcoming, I believe that tuning the parameters might be a good way to make the pipeline more general. This path will also help on the optional challenge. In addition, it will be interesting to explore the use some filters(Karman or Particle) that might help on detecting anomalies and noise in the data stream. 

Also, I which I had the take take more advantage of the numpy functions to make the code a bit better and more clear. 
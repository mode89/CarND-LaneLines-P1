# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the `draw_lines()` function.

My pipeline consisted of 5 steps.
First, I converted the images to grayscale, it helps to mask out dark part
of image, since we are interested only in the bright lane lines and want to
make it color agnostic. Then I applied the gaussian blur, which must
eliminate unnecesary noise from the image. With the next step I applied
canny filter to the blurred image. This step produced image with highlighted
edges of the lane lines and other bright areas. We want to focus only on
the lane lines of the current lane, that's why the next step cut away other
parts of the image by calling `region_of_interest()`, which leaved us only
the area corresponding to the current lane and the parts of the adjacent
lanes. Then I performed Hough transformation and extracted an array of
lines, some of which corresponded to the lane lines' edges.

In order to draw a single line on the left and right lanes, I modified the
`draw_lines()`. For each of the lines, extracted by the Hough
transformation, I calculated a polynomial of the first degree. I cut off
lines which have to be almost horizontal (the absolute value of the first
polynomial coefficient below 0.1). Then I assumed that lines, belonging to
the left lane line, reside in the left half of the image, and other lines
belongs to the right lane line. Based on this assumptions I made a list of
points corresponding to the left lane line and another list of points
corresponding to the right lane line. The I fit a polynomial of the first
degree to each of the lists, using `numpy.polyfit()`. These two polynomials
represent approximation of the two corresponding lane lines. I used partial
update to smooth the approximation, since sometimes Hough transformation
produces line segments lying outside of lane lines, which introduces
jittering of the output lines. Then I used these polynomials to draw two
solid lines on the output image.


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when there is another
car inside the region of interest or the lane surface is not uniformly
colored (debries, road marks, paintings, etc.). In this case Hough
transformation can produce lines, corresponding to the car and lying outside
of the lane line edges.

Another shortcoming could be what would happen when our car is changing
lane. In this case one of the lane lines would lie outside of the region of
interest.

Yet another shortcoming could be what would happen when the road is turning.
In this case one of the lane lines would cross the middle of the input
image.

Also `draw_lines()` draw lines only for straigh lane lines. At the turnes
lane lines aren't straight.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to employ polynomial fitting of a higher
degree during drawing lines and to draw a curve instead of straight line.

Another potential improvement could be to do the second iteration of Hough
transformation along the refined region of interest consisting of two areas,
where each of the areas tightly encompasses one of the corresponding lane
lines detected at first iteration of the Hough transformation.

Also we can employ one of the clustering algorighms to detect curvy lane
lines.

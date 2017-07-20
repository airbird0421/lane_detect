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

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

I have two pipelines, file P1.ipynb has the first one, and file P1-optional.ipynb has the second one. The first one works well with those two regular video streams, but not very well with the optional challenge. So I made some changes in the second pipeline, after which, it improves a lot for the optional challenge.

pipeline 1 (file P1.ipynb) has following steps:
1. apply grayscale transform by calling function grayscale();
2. apply Gaussian smoothing/blurring;
3. run Canny edge detection on the image to get a image with only outlines;
4. select a region of interest, which covers basically two lane lines. Mask out other regions;
5. Use Hough Transform to get all lines in the image
6. Overlay the lines got in step 5 to the original image.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function as following:
1. for each line, based on its slope, I can know it's for left lane (>0) or right lane(<0);
2. compute the average slope of both left lane lines and right lane lines;
3. for each line, extend the line in both ends, so that it reaches the top and bottom of region of interest (step 4 above); Calculate the x for both ends (y is fixed), and calculate the average of x for both ends;
4. draw a single line using the average x for both end and fixed y;

Need to take care of some special cases in calculations. First, some lines is not for the lane, but for some random objects on the road. So I filter all the lines based on absolute slope value in range (0.5, 2); Second, sometimes, there's not a single valid lane line (valid slope range) on an image. In this case, no average can be done to avoid divide by zero. 

In pipeline 2 (file P1-optional.ipynb), all the above apply. The main difference is that I added one more step (function selectcolor()) at the beginning of the pipeline. selectcolor() does the first processing by only keep yellow and white color in an image, which can greately remove the effects of tree shade.


### 2. Identify potential shortcomings with your current pipeline

For pipeline 1, as mentioned above, it doesn't work well if the road has some kinds of "noises", like the shade of tree, or cracks on the road, or different colors caused by road work, etc. In these cases, Hough Transform will give a lot of unexpected lines, which would cause the inaccuracy of the fina lane line. 

For pipeline 2, which can greately improve the issue in 1, but by only keep the yellow and white colors, it doesn't work well if the lane color itself is not so vivid, and the color selection would also result in some kind of lost of the lanes.


### 3. Suggest possible improvements to your pipeline

For issues mentioned above, one possible solution can be that further restricting the region of interest. Current region is quadrilateral. I think we can further limit the region to only include a little more than the lanes. This should be able to remove a lot of noise.

The second is to further improve/tune the color selectiong algorithm, such that only the useful color is kept and as less useful information as possible is lost;

The third is to further tune the Canny/Hough parameters to get better results.



##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/vehicle.png
[image2]: ./examples/hog.png
[image3]: ./examples/window.png
[image4]: ./examples/search.png
[image5]: ./examples/heatmap.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/bbox.png
[image8]: ./examples/spatial.png
[image9]: ./examples/hist.png
[image10]: ./examples/combo.png
[video1]: ./project_output.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the second code cell of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

Then the hog feature is extracted from each image. Here is the output image of HOG parameters of `orientations=9`, `pixels_per_cell=8` and `cells_per_block=2`:


![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.
At first, I extracted HOG feature only for gray images, which tends to make false positive predictions on the road. Then I decided to convert the image to YCrCb color space and extract HOG feature for each color channel so that the color information could also be utilized.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I used three features for the car classifcatioin. The first one is spatial binning of colors. I convert the image to 32x32x3 size and then transform the image matrix as a single vector of length 3027. 
![alt text][image8]

The second feature is color histogram and the third is HOG.
![alt text][image9]

At last, all these three features are combined and normalized.
![alt text][image10]

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to mainly search the lower half of the image with different sized windows. 

I used 4 different sized windows for search: 64x64, 100x100, 160x160 and 196x196. As the car move further away from camera, it becomes easier to lose target because not enough window could capture the car. So I increased the overlap ratio of 64x64 windows to 0.8 to ensure that the car will be captured.

![alt text][image3]

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on four scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]

To reject those false positive detections, the heatmap of previous 8 frames are stored in a queue. The threshold is calculated based on the average of these heatmaps.


### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here is the heatmap of the example image:

![alt text][image5]

### Here the resulting bounding boxes:
![alt text][image7]



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Problem1: Because the search window size is fixed, sometimes the bounding box may not cover exactly the car position.

Solution: This could be solved using more advanced detection techniques such as Faster-RCNN or yolo.

Problem2: The detection box may disappear in some frame.

Solution: Keep track of the box position could be helpful.


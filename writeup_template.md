

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/sliding_windows.jpg
[image4]: ./examples/sliding_window.jpg
[image5]: ./examples/bboxes_and_heat.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook .  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and see what happend with the trianed classifier, including every color space and different channel, for parameters of `orientations`, `pixels_per_cell` and `cells_per_block`, I did't change it more, because the final classification result is hard to be better. Finally I choose parameter as following list:

| parameter name         		|     value	        					| 
|:---------------------:|:---------------------------------------------:| 
| color_space       		| 'YCrCb' 							| 
| orient       		| 9							| 
| pix_per_cell       		| 8 							| 
| cell_per_block       		| 2 							| 
| hog_channel       		| "ALL"							| 

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

First I trained a linear SVM using sklearn, with 80% of data as training data and 20% as test data. As image feature I choose HOG feature, color histogram and also spatial feature. Then I got a result which achieve accuracy of 98.5%, but after try other type of SVM, I find SVC with `rbf` kernel can got 1% higher accuracy, but in this way there is a problem need noticing, it cost more time to train classifier and classify a image. Finally I choose the sencond way on my video, it cost 5 hours, but it indeed get a better result.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I use code in course's quiz code, it is very helpful. You can find it in 7th code cell. For start and stop point, I set y_start_stop = [400, 656] becuase this area is most likely to find cars. As for window size, there is a parameter `scale` which decide how we resize the original image that means in another way change the 'window size', I didn't change a lot for this part of code because it works well but I still think it is a little weird. I define a scale list and use a for-loop pass them to this function to use different value of scale (again, that means different window size). Then I find when I put too many values in this list, it cost too much time to process a single image. So I choose only 3 value: [1.5  1.8  2.1]. As for how much to overlap, I did't change the number of overlap cell, but with changing of scale, that changes too.

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on 3 scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

%%%图片￥￥￥￥￥￥￥￥￥￥￥

As for optimizing the performance of classifier I explain in q3: I use SVC with `rbf` kernel and got 1% higher accuracy, but it really cost much more time.

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here is a test image and its corresponding heatmaps:

![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from test image:

![alt text][image6]

### Here the resulting bounding boxes are drawn onto test image:

![alt text][image7]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further. First of all, I use SVM as classifier and with SVC with rbf kernel we can get better result. But the problem is it cost too much time. For this project it is okey because there is no require for timeout but when we face a real world problem it isn't a good choice. Sencond I didn't take advantage of consistency of frames, so the process seems to be unsoomth, please tell how to improve it in review!


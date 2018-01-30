## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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
[image1]: ./writeup_images/2.png
[image2]: ./writeup_images/1.png
[image3]: ./writeup_images/.png
[image4]: ./writeup_images/3.png
[image5]: ./writeup_images/5.png
[image6]: ./writeup_images/4.png
[image7]: ./writeup_images/6.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the code cell with the function called extract_features.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and chose YUV and I explored and tried different hog parameters.

Here is an example using the `YUV` color space and HOG parameters of `orientations=11`, `pixels_per_cell=(16, 16)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and I finally settled on usng the YUV color space taking ALL the channels, 11 orient 16 pixels per cell and 2 cells per block. My previous good preference was RGB and I still don't really find a huge difference between it and the YUV but the YUV just got me a better output in the shady areas were there used to be some false positives with the RGB. For the other parameters I just kept trying and testing parameters around the ones in the course quizzes and this ones got me the best results I was able to achieve.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM (can be found in the code cell named SVM Classifier) using the LinearSVC in sklearn with the vehicle and non-vehicle datasets provided in the course. The feature vector length: 1188, it took 3.42 Seconds to train the model with test accuracy of 0.971.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to take an approach were I will do a window search in every horizontal stripe with a different window size based on the assumption that as the cars get closer thet are bigger and when they are far they are smaller. So I created 8 different window sizes for the video. The closer the window the higher value I used in the scale.

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I implemented a history of non-false positive frames and used it with the heatmap to decrease the false positives. It works best on a video as images do not have a history.

![alt text][image4]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the bounding boxes found in the last 3 frames and used them to improve the output of the heatmap as they increase the number of boxes on the cars. For combining the overlapping bounding boxes, I used the same way used in the quizzes.
Here's an example result showing the heatmap from a series of frames of video, the result of the history based detection:

### Here are six frames and their corresponding heatmaps:

![alt text][image5]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image7]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The problem I faced was that the windows detecting the cars are not stable as in they do not work in all frames. In addition, the car can be detected more than one time as the features get detected as different cars. Also there was some false positives in shadow such as the tree part in the video. I think I may need a stronger classifier than the SVC, maybe using pretrained weights from something like DarkNet or YOLO, this would make the output much better.


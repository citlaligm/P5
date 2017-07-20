**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Implement a sliding-window technique and use a trained classifier to search for vehicles in images.
* Run your pipeline on a video stream and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car_not_car.png
[image2]: ./output_images/hog_car_64.png
[image3]: ./output_images/hog_nocar_64.png
[image4]: ./output_images/sliding_window.png
[image5]: ./output_images/pipeline.png
[image6]: ./output_images/example_output.png

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.    

This is the README for this project

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the code cell #10 of the IPython notebook `Test.ipynb`


I started by reading in all the `vehicle` and `non-vehicle` images.  The following image shows an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YUV` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


***HOG for Car images***
![alt text][image2]


***HOG for Non Car images***

![alt text][image3]


#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and I found that good combination of HOG parameters was the following:


| Parameter        | Value   | 
|:----------------:|:--------| 
| Color            | YUV     | 
| Orientations     | 9       |
| Pixels_per_cell  | 64x64   |
| Cells_per_block  | 2x2     |
| Channel          | 0       |


#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using ***LinearSVC*** from `sklearn`, I used the HOG features obtained in the previous step, I also included color features, which produced 14,244 features. I shuffled and splitted the data on a training set and a test set using `train_test_split` and after training my classifier I obtained a ***Test Accuracy of 0.9825***

The code for this section can be found in the code cell #11 of the IPython notebook `Test.ipynb`

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to create windows of 2 different sizes, the following table with explain the ranges and sizes for the windows.

| Window Size | Range X   | Range Y  | 
|:-----------:|:----------|:---------| 
| 90x90       | 610,1110  | 400,490  | 
| 150x150     | 530,1280  | 380, 550 | 


The code for this section can be found in the code cell #15 on lines 15 to 24 of the IPython notebook `Test.ipynb`. The following image shows an example with all the windows drawn.

![alt text][image4]


#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to try to minimize false positives and reliably detect cars?

Finally I searched on two scales of windows, using YUV 1-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image5]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](https://youtu.be/0-LkvLX9XNU)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I created a class to keep track of the cars found. Once I had all the windows detected as positive cars, I iterated over the list of windows and if one of the windows was already on the list of cars, then I merged this new window and calculated the new position of the car.

Then I checked if the difference between the current frame and the frame where the windows was captured is less than 3 then I'll keep it since I'm pretty sure it was a car and not a false positive. Finally, I checked again the resultant windows to check if the there were still windows that belonged to the same car and merged them.

The code for this section can be found in the code cell #15 from lines 35 to 84 of the IPython notebook `Test.ipynb`


---

###Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I had problems determining where to draw the windows, the correct size, I did a lot of experimentation on this, but finally I found a good combination I think. I think the use of a class to keep track of the bounding boxes that were carsworked very well

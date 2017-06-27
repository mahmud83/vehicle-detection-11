## Vehicle detection Project
---

#### Project writeup

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./writeup_dir/hog1.png
[image2]: ./writeup_dir/hog2.jpg
[image3]: ./writeup_dir/slide1.jpg
[image4]: ./writeup_dir/pipeline1.jpg
[image5]: ./writeup_dir/bboxes_and_heat.png
[image6]: ./writeup_dir/labels_map.png
[image7]: ./writeup_dir/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points

---

### Writeup / README

##### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.

You're reading it!

##### Histogram of Oriented Gradients (HOG)

##### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

I implemented HOG feature extraction in a class called FeatureExtractor which is located in Code block 3. The above said class implements functions from lessons_functions.py like color_hist(), bin_spatial(), get_hog_features(), extract_features().

Before extracting features from input or test data, the input image is fed through pipeline which creates sliding windows of selected scale and quadrant.
Then this windows are then interfaced with classifier which returns values corresponding if image window contains car or non-car.

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random image from 'vehicle' classes and displayed it to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=11`, `pixels_per_cell=(16, 16)` and `cells_per_block=(2, 2)`:

![alt text][image1]

![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I started with the combination mentioned in classroom lessons. But using them for video pipeline resulted in false positives. So I modified the orientation, pixeles_per_cell to new values. I was also able to save 1 sec of processing time using this values per iteration, which is insignificant improvement compared to earlier bottleneck of 4 secs.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I have implemented code to train the classifier in function train_classifiere() in code block number 6.
I loaded all cars and non car images filename into a local buffer. Using the FeatureExtract class and its functions, I extracted features for each of the images and added them to a list. Then the features are used to train and test the LinearSVC. I used all color and HOG features for training the classifier.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I have implemented function slide_window() in code block number 4 as a part of SearchandClassify class. This class acts as additional layer of encapsulation over basic FeatureExtractor class.
For this project, we are given input video in which car is being driven in leftmost lane and all other vehicles to be detected are in right lane. Additionally, the chances of finding cars are high in lower half of the image. Hence, the area of interest of video image is in right bottom quadrant and hence we choose window parameters to reflect the same.
Since the percentage of window overlap directly affects the number of windows to be searched, I carefully chose the value by doing some experiments.

![alt text][image3]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I used the sample images from test images directory to check if each stage of vehicle detection is working as expected or not. In order to remove the false positive, I followed the approach of adding heat to all pixels of hot windows and taking a subset of that mega window using particular threshold. Once, I had threshold result, I modified the threshold value to reflect accurate detection of cars in images/video.

![alt text][image4]

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

Here's a [link to my video result](./output_images/project_video_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
![alt text][image6]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image7]

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.

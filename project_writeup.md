## Project Writeup

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
[image1]: ./output_images/car_not_car.png
[image2]: ./output_images/HOG_example.png
[image3]: ./output_images/sliding_windows.png
[image4]: ./output_images/sliding_window_reduced_false_positive.png
[image5]: ./output_images/bboxes_and_heat.png
[image6]: ./output_images/labels_map.png
[image7]: ./output_images/test_pipeline_result.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook (or in lines # through # of the file called `some_file.py`).  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of a grid of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the blue channel of the`RGB` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)` on one of the image:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

The HOG parameters are selected by trying a few combinations and comparing the trained SVM model's perfomance against the test dataset. Overall, the difference between various color spaces are close (around 98% accuracy) except for RGB space where the accuracy dropped to around 96%. This is probably because the same issue of different light conditions we have encountered in the lane line detection project. I choose YUV space eventually since it achieves the highest accuracy of 98.45% (using the default SVM parameters):
- Color Space: YUV
- orient: 9 (larger numbers doesn't help much and even makes performance worse)
- pixels_per_cell = (8,8)
- cells_per_block = (2,2)

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using the HOG features of all channels. I did extract the color features and stacked with the HOG features but somehow it was not working during the prediction process and I haven't figured out why. This is something I'll follow up with after the project submission.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The search was only performed on the lower part of the image as we wouldn't expect cars on the top of a tree or in the sky (well, maybe after the fly car program students graduate;) ). I chose a range of scales from 1.0 to 3.5 with a 0.5 interval in order to cover as many cases as possible. The overlap windows are 75% (since we are using 2 cells per step out of 8 cells).


#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YUV 3-channel HOG features in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image3]

We can see that there are some false positives in the detection, which we can use heat map to reduce. Here we use a threshold of 2 and obtained the following results:

The heatmap:
![alt text][image5]

The labels detected:
![alt text][image6]

The detection with the label bounding box:
![alt_text][image4]

If we apply this pipeline to our test images, we have the following result:
![alt_text][image7]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
I first applied the pipeline on the test video. Without considering bounding boxes from previous frames, it seems a bit unstable on the detection. Here's the link to the [test video output](./test_video_output.mp4).

If we can consider the boxes from previous frames (like what we did in the lane line detection), the performance is better. Here's the link to the [second test video output](./test_video_output_2.mp4).

Here's a [link to my video result](./project_video_out.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

As mentioned above, this is done by applying the heatmap thresholding in the [Remove False Positives using heatmap section of the notebook](./project-vechicle-detection.ipynb#Remove-False-Positives-by-using-HeatMap). 

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I'm still trying to figure out what have gone wrong in my code to combine the HOG features with the color-based features in the prediction process. Using the color information should help further improve the classification. 

There are still false positives even after applying the heat map filtering. If we use more training data (for example the udacity labeled dataset), we should be able to get this improved.


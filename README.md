# **Finding Lane Lines on the Road** 

## Pipeline


[//]: # (Image References)

[image1]: ./writeup_images/1_blur.png "Blur"
[image2]: ./writeup_images/2_threshold.png "Thresholds"
[image3]: ./writeup_images/3_canny.png "Canny"
[image4]: ./writeup_images/4_area.png "Area"
[image5]: ./writeup_images/5_hough_and_selection.png "Hough"
[image6]: ./writeup_images/6_interpol.png "Final"
[image7]: ./debug_videos/solidWhiteRight_debug.gif "White"
[image8]: ./debug_videos/solidYellowLeft_debug.gif "Yellow"
[image9]: ./debug_videos/challenge_debug.gif "Challenge"



### 1: Blur filter

First step of the pipeline is smoothing the image with a gaussian blur, a kernel size of `11` was chosen by trial and error which eliminates most of the noise and helps reducing the ammount of detected lines that are not part of any road lanes.

![alt text][image1]

### 2: Image thresholding

Adaptative thresholding is applied to help sharpen images that have different lightning conditions, where the threshold value is the weighted sum of the neighbourhood values.   

![alt text][image2]

### 3: Canny edge detector

Result after applying the canny edge detection, the thresholds are calculated each frame based on the current median value of the image.

`LowThreshold = 0.66*median`

`HighThreshold = 1.33*median`

![alt text][image3]

### 4: Area of interest

A simple polygon that captures the lower `40%` of the image is used (`blue_cyan` in the previous image)

![alt text][image4]

### 5: Hough lines/Candidate Selection 

Result after the Hough line transformation with a low value `(40%)` for threshold detection and `15px` for max line gap, which results in a high amount of detected lines. A filtering process is then done to exclude outliers:

 - Lines with too high (close to vertical) or too low (close to horizontal) slope are filtered out, the tresholds are calculated relative to the diagonal of the image;
 
 - Very short lines are filtered out;
 - The `m` and `b` parameters of all lines are calculated and put in a `KDTree`, then all lines that are too distant/different from the extrapolated line (from the previous frame) are filtered out. If there's no previous frame the reference line is just the median of all lines;
 
 - The remaining lines are then averaged resulting in the ``m` and  `b` values the extrapolation.

`Note:`  The green lines are the accepted lines, the blue cyan are the rejected lines, in the bellow image there are no rejections since all lines are pretty close and similar (considering they are separated in left and right). However in the [videos](#videos) with `DEBUG = True` it's more noticeable.

![alt text][image5]

### 6: Average/Extrapolation

Extrapolation is done for the left and right lane, a circular buffer is used to store the extrapolated lines from the `N` previous frames, the plotted final red lines are simply the averages of the buffers. The buffering helps smoothing the line jumps in the videos. 

![alt text][image6]




## Videos

#### Solid White Right

![alt text][image7]

#### Solid Yellow Left

![alt text][image8]

#### Challenge

![alt text][image9]

---
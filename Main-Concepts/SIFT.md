# Introduction to SIFT (Scale-Invariant Feature Transform)

## Goal
In this chapter:

- We will learn about the concepts of the SIFT algorithm.
- We will learn to find SIFT Keypoints and Descriptors.

## Theory
In the last couple of chapters, we saw some corner detectors like Harris, etc. They are rotation-invariant, which means, even if the image is rotated, we can find the same corners. It is obvious because corners remain corners in a rotated image as well. But what about scaling? A corner may not be a corner if the image is scaled. For example, check a simple image below. A corner in a small image within a small window is flat when it is zoomed in the same window. So Harris corner is not scale-invariant.

![image](https://docs.opencv.org/4.x/sift_scale_invariant.jpg)

In 2004, D. Lowe, University of British Columbia, came up with a new algorithm, Scale Invariant Feature Transform (SIFT), in his paper, *Distinctive Image Features from Scale-Invariant Keypoints*, which extracts keypoints and computes their descriptors. *(This paper is easy to understand and considered to be the best material available on SIFT. This explanation is just a short summary of this paper)*.

There are mainly four steps involved in the SIFT algorithm. We will see them one by one.

### 1. Scale-space Extrema Detection
From the image above, it is obvious that we can't use the same window to detect keypoints with different scales. It is OK with a small corner. But to detect larger corners, we need larger windows. For this, scale-space filtering is used. In it, the Laplacian of Gaussian is found for the image with various σ values. LoG acts as a blob detector that detects blobs in various sizes due to changes in σ. In short, σ acts as a scaling parameter. For example, in the above image, a Gaussian kernel with low σ gives a high value for a small corner, while a Gaussian kernel with high σ fits well for a larger corner. So, we can find the local maxima across the scale and space, which gives us a list of σ values, meaning there is a potential keypoint at (x,y) at a particular scale.

But this LoG is a little costly, so the SIFT algorithm uses the Difference of Gaussians, which is an approximation of LoG. The Difference of Gaussian is obtained as the difference of Gaussian blurring of an image with two different σ values, let it be σ and kσ. This process is done for different octaves of the image in the Gaussian Pyramid. It is represented in the image below:

![image](https://docs.opencv.org/4.x/sift_dog.jpg)

Once these DoG are found, images are searched for local extrema over scale and space. For example, one pixel in an image is compared with its 8 neighbors, as well as 9 pixels in the next scale and 9 pixels in the previous scales. If it is a local extrema, it is a potential keypoint. It basically means that the keypoint is best represented in that scale. It is shown in the image below:

![image](https://docs.opencv.org/4.x/sift_local_extrema.jpg)

Regarding different parameters, the paper gives some empirical data which can be summarized as:
- Number of octaves = 4
- Number of scale levels = 5
- Initial σ, k, etc., as optimal values.

### 2. Keypoint Localization
Once potential keypoint locations are found, they have to be refined to get more accurate results. They used the Taylor series expansion of the scale space to get a more accurate location of extrema, and if the intensity at this extrema is less than a threshold value (0.03 as per the paper), it is rejected. This threshold is called `contrastThreshold` in OpenCV.

DoG has a higher response for edges, so edges also need to be removed. For this, a concept similar to the Harris corner detector is used. They used a 2x2 Hessian matrix (H) to compute the principal curvature. We know from the Harris corner detector that for edges, one eigenvalue is larger than the other. So here they used a simple function:

If this ratio is greater than a threshold, called `edgeThreshold` in OpenCV, that keypoint is discarded. It is given as 10 in the paper.

So it eliminates any low-contrast keypoints and edge keypoints, and what remains is strong interest points.

### 3. Orientation Assignment
Now an orientation is assigned to each keypoint to achieve invariance to image rotation. A neighborhood is taken around the keypoint location depending on the scale, and the gradient magnitude and direction are calculated in that region. An orientation histogram with 36 bins covering 360 degrees is created. It is weighted by gradient magnitude and a Gaussian-weighted circular window with σ equal to 1.5 times the scale of the keypoint. The highest peak in the histogram is taken, and any peak above 80% of it is also considered to calculate the orientation. It creates keypoints with the same location and scale, but different directions. This contributes to the stability of matching.

### 4. Keypoint Descriptor
Now the keypoint descriptor is created. A 16x16 neighborhood around the keypoint is taken. It is divided into 16 sub-blocks of 4x4 size. For each sub-block, an 8-bin orientation histogram is created. So a total of 128 bin values are available. It is represented as a vector to form the keypoint descriptor. In addition to this, several measures are taken to achieve robustness against illumination changes, rotation, etc.

### 5. Keypoint Matching
Keypoints between two images are matched by identifying their nearest neighbors. But in some cases, the second closest match may be very near to the first. It may happen due to noise or other reasons. In that case, the ratio of the closest-distance to the second-closest distance is taken. If it is greater than 0.8, they are rejected. It eliminates around 90% of false matches while discarding only 5% correct matches, as per the paper.

This is a summary of the SIFT algorithm. For more details and understanding, reading the original paper is highly recommended.

## SIFT in OpenCV
Now let's see the SIFT functionalities available in OpenCV. Note that these were previously only available in the `opencv_contrib` repo, but the patent expired in 2020. So they are now included in the main repo. Let's start with keypoint detection and draw them. First, we have to construct a SIFT object. We can pass different parameters to it which are optional and are well explained in the docs.

```python
import numpy as np
import cv2 as cv

img = cv.imread('home.jpg')
gray= cv.cvtColor(img,cv.COLOR_BGR2GRAY)

sift = cv.SIFT_create()
kp = sift.detect(gray,None)

img=cv.drawKeypoints(gray,kp,img)

cv.imwrite('sift_keypoints.jpg',img)
```

The `sift.detect()` function finds the keypoints in the images. You can pass a mask if you want to search only a part of the image. Each keypoint is a special structure with many attributes like its (x, y) coordinates, size of the meaningful neighborhood, angle specifying its orientation, response specifying the strength of keypoints, etc.

OpenCV also provides the `cv.drawKeyPoints()` function which draws small circles at the locations of keypoints. If you pass a flag, `cv.DRAW_MATCHES_FLAGS_DRAW_RICH_KEYPOINTS`, it will draw a circle with the size of the keypoint and will even show its orientation. See the example below.

```python
img=cv.drawKeypoints(gray,kp,img,flags=cv.DRAW_MATCHES_FLAGS_DRAW_RICH_KEYPOINTS)
cv.imwrite('sift_keypoints.jpg',img)
```

See the two results below:

![image](https://docs.opencv.org/4.x/sift_keypoints.jpg)

Now, to calculate the descriptor, OpenCV provides two methods:

1. Since you already found keypoints, you can call `sift.compute()` which computes the descriptors from the keypoints we have found.
   ```python
   kp, des = sift.compute(gray, kp)
   ```

2. If you didn't find keypoints, directly find keypoints and descriptors in a single step with the function, `sift.detectAndCompute()`.
   We will see the second method:

   ```python
   sift = cv.SIFT_create()
   kp, des = sift.detectAndCompute(gray,None)
   ```

Here `kp` will be a list of keypoints and `des` is a numpy array of shape `(Number of Keypoints, 128)`.

So we got keypoints, descriptors, etc. Now we want to see how to match keypoints in different images. That we will learn in the coming chapters.
```

Replace the placeholder `image_link_here` with the actual links to your images if you have them, or insert the images using the correct Markdown syntax for your images.

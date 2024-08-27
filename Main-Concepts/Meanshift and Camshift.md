# Meanshift and Camshift

## Goal
In this chapter,

We will learn about the Meanshift and Camshift algorithms to track objects in videos.

## Meanshift

### Meanshift

- Meanshift involves a set of points, which could be a pixel distribution like a histogram backprojection.
- You start with a small window (like a circle).
- The goal is to move this window to the area with the highest pixel density or the most points.

`Note`: Backprojection in histograms refers to a technique used in computer vision and image processing to highlight regions in an image that have a similar color distribution to a given target histogram. 
<div align ="center'><img src = "https://docs.opencv.org/5.x/meanshift_basics.jpg"></div>

- The initial window is shown as a blue circle labeled "C1".
- The original center of this window is marked as "C1_o" with a blue rectangle.
- The centroid of the points inside this window is marked as "C1_r" with a small blue circle.
- The original center and the centroid don't match, so move the window to align with the centroid.
- Repeat this process until the window's center matches the centroid (or is very close).
- The final window with the maximum pixel distribution is marked as a green circle labeled "C2," which contains the most points.

This process is demonstrated on a static image below:

<div align ="center"><img src ="https://docs.opencv.org/5.x/meanshift_face.gif"></div>

- The histogram backprojected image and the initial target location are usually provided.
- When the object moves, this movement is reflected in the histogram backprojected image.
- The meanshift algorithm then moves the window to the new location with the highest pixel density.

## Meanshift in OpenCV
To use meanshift in OpenCV, first we need to setup the target, find its histogram so that we can backproject the target on each frame for calculation of meanshift. We also need to provide an initial location of window. For histogram, only Hue is considered here. Also, to avoid false values due to low light, low light values are discarded using `cv.inRange()` function.

**Downloadable code:** [Click here](https://github.com/opencv/opencv/blob/5.x/samples/python/tutorial_code/video/meanshift/meanshift.py)

**Code at a glance:**
```python
import numpy as np
import cv2 as cv
import argparse

parser = argparse.ArgumentParser(description='This sample demonstrates the meanshift algorithm. \
                                              The example file can be downloaded from: \
                                              https://www.bogotobogo.com/python/OpenCV_Python/images/mean_shift_tracking/slow_traffic_small.mp4')
parser.add_argument('image', type=str, help='path to image file')
args = parser.parse_args()

cap = cv.VideoCapture(args.image)

# take first frame of the video
ret, frame = cap.read()

# setup initial location of window
x, y, w, h = 300, 200, 100, 50  # simply hardcoded the values
track_window = (x, y, w, h)

# set up the ROI for tracking
roi = frame[y:y+h, x:x+w]
hsv_roi = cv.cvtColor(roi, cv.COLOR_BGR2HSV)
mask = cv.inRange(hsv_roi, np.array((0., 60., 32.)), np.array((180., 255., 255.)))
roi_hist = cv.calcHist([hsv_roi], [0], mask, [180], [0, 180])
cv.normalize(roi_hist, roi_hist, 0, 255, cv.NORM_MINMAX)

# Setup the termination criteria, either 10 iteration or move by at least 1 pt
term_crit = (cv.TERM_CRITERIA_EPS | cv.TERM_CRITERIA_COUNT, 10, 1)

while True:
    ret, frame = cap.read()

    if ret == True:
        hsv = cv.cvtColor(frame, cv.COLOR_BGR2HSV)
        dst = cv.calcBackProject([hsv], [0], roi_hist, [0, 180], 1)

        # apply meanshift to get the new location
        ret, track_window = cv.meanShift(dst, track_window, term_crit)

        # Draw it on image
        x, y, w, h = track_window
        img2 = cv.rectangle(frame, (x, y), (x+w, y+h), 255, 2)
        cv.imshow('img2', img2)

        k = cv.waitKey(30) & 0xff
        if k == 27:
            break
    else:
        break
```
Three frames in a video I used is given below:

<div align ="center"><img src ="https://docs.opencv.org/5.x/meanshift_result.jpg"></div>

## Camshift

Did you closely watch the last result? There is a problem. Our window always has the same size whether the car is very far or very close to the camera. That is not good. We need to adapt the window size with size and rotation of the target. Once again, the solution came from "OpenCV Labs" and it is called CAMshift (Continuously Adaptive Meanshift) published by Gary Bradsky in his paper "Computer Vision Face Tracking for Use in a Perceptual User Interface" in 1998 [39].

It applies meanshift first. Once meanshift converges, it updates the size of the window and calculates the orientation of the best fitting ellipse to it. Again it applies the meanshift with the new scaled search window and previous window location. The process continues until the required accuracy is met.

<div align ="center"><img src ="https://docs.opencv.org/5.x/camshift_face.gif"></div>

## Camshift in OpenCV
It is similar to meanshift, but returns a rotated rectangle (that is our result) and box parameters (used to be passed as search window in next iteration). See the code below:

**Downloadable code:** [Click here](https://github.com/opencv/opencv/blob/5.x/samples/python/tutorial_code/video/meanshift/camshift.py)

**Code at a glance:**
```python
import numpy as np
import cv2 as cv
import argparse

parser = argparse.ArgumentParser(description='This sample demonstrates the camshift algorithm. \
                                              The example file can be downloaded from: \
                                              https://www.bogotobogo.com/python/OpenCV_Python/images/mean_shift_tracking/slow_traffic_small.mp4')
parser.add_argument('image', type=str, help='path to image file')
args = parser.parse_args()

cap = cv.VideoCapture(args.image)

# take first frame of the video
ret, frame = cap.read()

# setup initial location of window
x, y, w, h = 300, 200, 100, 50  # simply hardcoded the values
track_window = (x, y, w, h)

# set up the ROI for tracking
roi = frame[y:y+h, x:x+w]
hsv_roi = cv.cvtColor(roi, cv.COLOR_BGR2HSV)
mask = cv.inRange(hsv_roi, np.array((0., 60., 32.)), np.array((180., 255., 255.)))
roi_hist = cv.calcHist([hsv_roi], [0], mask, [180], [0, 180])
cv.normalize(roi_hist, roi_hist, 0, 255, cv.NORM_MINMAX)

# Setup the termination criteria, either 10 iteration or move by at least 1 pt
term_crit = (cv.TERM_CRITERIA_EPS | cv.TERM_CRITERIA_COUNT, 10, 1)

while True:
    ret, frame = cap.read()

    if ret == True:
        hsv = cv.cvtColor(frame, cv.COLOR_BGR2HSV)
        dst = cv.calcBackProject([hsv], [0], roi_hist, [0, 180], 1)

        # apply camshift to get the new location
        ret, track_window = cv.CamShift(dst, track_window, term_crit)

        # Draw it on image
        pts = cv.boxPoints(ret)
        pts = np.int0(pts)
        img2 = cv.polylines(frame, [pts], True, 255, 2)
        cv.imshow('img2', img2)

        k = cv.waitKey(30) & 0xff
        if k == 27:
            break
    else:
        break
```
Three frames of the result are shown below:

<div align ="center"><img src ="https://docs.opencv.org/5.x/camshift_result.jpg"></div>

## Additional Resources
- [French Wikipedia page on Camshift](https://fr.wikipedia.org/wiki/CAMShift) (The two animations are taken from there)
- Bradski, G.R., "Real-time face and object tracking as a component of a perceptual user interface," Applications of Computer Vision, 1998. WACV '98. Proceedings., Fourth IEEE Workshop on, vol., no., pp.214-219, 19-21 Oct 1998

## Exercises
OpenCV comes with a Python sample for an interactive demo of camshift. Use it, hack it, understand it.
```

Replace `"path-to-image"` and `"path-to-code"` with the actual paths or URLs to your images and code files.

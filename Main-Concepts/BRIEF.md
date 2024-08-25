# BRIEF (Binary Robust Independent Elementary Features)

## Goal
In this chapter, we will see the basics of the BRIEF algorithm.

## Theory
We know SIFT uses a 128-dimensional vector for descriptors. Since it uses floating point numbers, it requires approximately 512 bytes. Similarly, SURF also takes a minimum of 256 bytes (for 64 dimensions). Creating such vectors for thousands of features consumes a lot of memory, which is not feasible for resource-constrained applications, especially for embedded systems. Larger memory usage also results in longer matching times.

However, not all these dimensions are necessary for actual matching. We can compress them using several methods like PCA, LDA, etc. Other methods, such as hashing using LSH (Locality Sensitive Hashing), are also used to convert these SIFT descriptors from floating point numbers to binary strings. These binary strings are used to match features using Hamming distance, which provides better speed because finding the Hamming distance involves just applying XOR and bit counting, which are very fast in modern CPUs with SSE instructions. However, we still need to find the descriptors first before applying hashing, which doesn't solve our initial memory problem.

BRIEF provides a shortcut by allowing us to find binary strings directly without first finding the descriptors. It takes a smoothed image patch and selects a set of `(x,y)` location pairs in a unique way (explained in the paper). Then, some pixel intensity comparisons are made on these location pairs. For example, let the first location pairs be `p` and `q`. If `I(p) < I(q)`, the result is 1; otherwise, it is 0. This process is applied to all the `nd` location pairs to get an `nd`-dimensional bitstring.

This `nd` can be 128, 256, or 512. OpenCV supports all of these, but by default, it is 256 (OpenCV represents it in bytes, so the values will be 16, 32, and 64). Once you get this, you can use Hamming Distance to match these descriptors.

An important point to note is that BRIEF is a feature descriptor; it doesn't provide any method to find the features. Therefore, you will need to use other feature detectors like SIFT, SURF, etc. The paper recommends using CenSurE, which is a fast detector, and BRIEF works even slightly better for CenSurE points than for SURF points.

In short, BRIEF is a faster method for feature descriptor calculation and matching. It also provides a high recognition rate unless there is large in-plane rotation.

## STAR (CenSurE) in OpenCV
STAR is a feature detector derived from CenSurE. Unlike CenSurE, which uses polygons like squares, hexagons, and octagons to approximate a circle, STAR emulates a circle with two overlapping squares: one upright and one rotated by 45 degrees. These polygons are bi-level, meaning they can be seen as polygons with thick borders. The borders and the enclosed area have weights of opposing signs. This has better computational characteristics than other scale-space detectors and is capable of real-time implementation. In contrast to SIFT and SURF, which find extrema at sub-sampled pixels (compromising accuracy at larger scales), CenSurE creates a feature vector using full spatial resolution at all scales in the pyramid.

## BRIEF in OpenCV
The code below shows the computation of BRIEF descriptors with the help of the CenSurE detector.

> **Note:** You need `opencv-contrib` to use this.

```python
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt

img = cv.imread('simple.jpg', cv.IMREAD_GRAYSCALE)

# Initiate FAST detector
star = cv.xfeatures2d.StarDetector_create()

# Initiate BRIEF extractor
brief = cv.xfeatures2d.BriefDescriptorExtractor_create()

# Find the keypoints with STAR
kp = star.detect(img, None)

# Compute the descriptors with BRIEF
kp, des = brief.compute(img, kp)

print(brief.descriptorSize())
print(des.shape)
```
### Checkout the results below:

<div align ="center"><img src ="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/25.png"></div>

## Video implemention

To convert the given code from detecting keypoints and descriptors in an image to performing video detection using BRIEF, you need to modify the code to process video frames instead of a static image. Here’s a brief explanation and the updated code:

**Steps for Video Detection:**
1. Open a video stream using OpenCV.
2. Loop through each frame in the video.
3. Apply the keypoint detection and descriptor extraction on each frame.
4. Display the keypoints on the video in real-time.

**Updated Code for Video Detection with BRIEF:**

```python
import cv2 as cv

# Open video capture (0 for webcam, or provide video file path)
cap = cv.VideoCapture(0)  # Replace 0 with a video file path if needed

# Check if video capture is successful
if not cap.isOpened():
    print("Error opening video stream or file")
    exit()

# Initiate SIFT detector (since STAR is outdated)
star = cv.SIFT_create()

# Initiate BRIEF extractor
brief = cv.xfeatures2d.BriefDescriptorExtractor_create()

while True:
    # Capture frame-by-frame
    ret, frame = cap.read()
    if not ret:
        print("Failed to grab frame")
        break

    # Convert frame to grayscale
    gray_frame = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)

    # Find the keypoints with STAR
    kp = star.detect(gray_frame, None)

    # Compute the descriptors with BRIEF
    kp, des = brief.compute(gray_frame, kp)

    # Draw keypoints on the frame
    frame_kp = cv.drawKeypoints(frame, kp, None, flags=cv.DRAW_MATCHES_FLAGS_DRAW_RICH_KEYPOINTS)

    # Display the resulting frame with keypoints
    cv.imshow("Keypoints", frame_kp)

    # Press 'q' to exit the video window
    if cv.waitKey(1) & 0xFF == ord('q'):
        break

# Release the video capture and close windows
cap.release()
cv.destroyAllWindows()
```

**Explanation:**
- **Video Capture:** `cap = cv.VideoCapture(0)` opens the default webcam. You can replace `0` with a video file path to process a saved video.
- **Looping through frames:** Each frame is grabbed and processed in a loop until the video ends or the user exits.
- **Processing:** The keypoint detection and descriptor computation happen for each frame.
- **Displaying Results:** The keypoints are drawn on each frame and displayed in real-time.

**To Run the Code:**
Ensure you have OpenCV with extra modules installed (`opencv-contrib-python`) for the BRIEF extractor.
---

# FAST Algorithm for Corner Detection

## Goal

In this chapter:

- We will understand the basics of FAST algorithm.
- We will find corners using OpenCV functionalities for FAST algorithm.

## Theory

We saw several feature detectors, and many of them are really good. But when looking from a real-time application point of view, they are not fast enough. One best example would be SLAM (Simultaneous Localization and Mapping) mobile robot, which has limited computational resources.

As a solution to this, FAST (Features from Accelerated Segment Test) algorithm was proposed by Edward Rosten and Tom Drummond in their paper "Machine learning for high-speed corner detection" in 2006 (later revised in 2010). A basic summary of the algorithm is presented below. Refer to the original paper for more details (All the images are taken from the original paper).

### Feature Detection using FAST

1. Select a pixel **p** in the image to be identified as an interest point or not. Let its intensity be **Ip**.
2. Select an appropriate threshold value **t**.
3. Consider a circle of 16 pixels around the pixel under test (as shown in the image below).

   ![image](https://docs.opencv.org/4.x/fast_speedtest.jpg)

4. Now, the pixel **p** is a corner if there exists a set of **n** contiguous pixels in the circle (of 16 pixels) which are all brighter than **Ip + t**, or all darker than **Ip - t**. **n** was chosen to be 12.
5. A high-speed test was proposed to exclude a large number of non-corners. This test examines only the four pixels at 1, 9, 5, and 13 (First 1 and 9 are tested if they are too brighter or darker. If so, then checks 5 and 13). If **p** is a corner, then at least three of these must all be brighter than **Ip + t** or darker than **Ip - t**. If neither of these is the case, then **p** cannot be a corner. The full segment test criterion can then be applied to the passed candidates by examining all pixels in the circle.

   This detector in itself exhibits high performance, but there are several weaknesses:

   - It does not reject as many candidates for n < 12.
   - The choice of pixels is not optimal because its efficiency depends on the ordering of the questions and distribution of corner appearances.
   - Results of high-speed tests are thrown away.
   - Multiple features are detected adjacent to one another.

First 3 points are addressed with a machine learning approach. The last one is addressed using non-maximal suppression.

### Machine Learning a Corner Detector

1. Select a set of images for training (preferably from the target application domain).
2. Run the FAST algorithm on every image to find feature points.
3. For every feature point, store the 16 pixels around it as a vector. Do it for all the images to get feature vector **P**.
4. Each pixel (say **p**i) in these 16 pixels can have one of the following three states:

   ![image](https://docs.opencv.org/4.x/fast_eqns.jpg)

   Depending on these states, the feature vector **P** is subdivided into 3 subsets, **S**d, **S**s, **S**b.

5. Define a new boolean variable, **Kp**, which is true if **p** is a corner and false otherwise.
6. Use the ID3 algorithm (decision tree classifier) to query each subset using the variable **Kp** for the knowledge about the true class. It selects the **p**i which yields the most information about whether the candidate pixel is a corner, measured by the entropy of **Kp**.
7. This is recursively applied to all the subsets until its entropy is zero.
8. The decision tree so created is used for fast detection in other images.

### Non-maximal Suppression

Detecting multiple interest points in adjacent locations is another problem. It is solved by using Non-maximum Suppression.

- Compute a score function, **V** for all the detected feature points. **V** is the sum of absolute differences between **Ip** and 16 surrounding pixels' values.
- Consider two adjacent keypoints and compute their **V** values.
- Discard the one with the lower **V** value.

### Summary

- It is several times faster than other existing corner detectors.
- But it is not robust to high levels of noise. It is dependent on a threshold.

## FAST Feature Detector in OpenCV

It is called like any other feature detector in OpenCV. If you want, you can specify the threshold, whether non-maximum suppression is to be applied or not, the neighborhood to be used, etc.

For the neighborhood, three flags are defined: `cv.FAST_FEATURE_DETECTOR_TYPE_5_8`, `cv.FAST_FEATURE_DETECTOR_TYPE_7_12`, and `cv.FAST_FEATURE_DETECTOR_TYPE_9_16`. Below is a simple code on how to detect and draw the FAST feature points.

```python
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt

img = cv.imread('blox.jpg', cv.IMREAD_GRAYSCALE) # `<opencv_root>/samples/data/blox.jpg`

# Initiate FAST object with default values
fast = cv.FastFeatureDetector_create()

# find and draw the keypoints
kp = fast.detect(img,None)
img2 = cv.drawKeypoints(img, kp, None, color=(255,0,0))

# Print all default params
print( "Threshold: {}".format(fast.getThreshold()) )
print( "nonmaxSuppression:{}".format(fast.getNonmaxSuppression()) )
print( "neighborhood: {}".format(fast.getType()) )
print( "Total Keypoints with nonmaxSuppression: {}".format(len(kp)) )

cv.imwrite('fast_true.png', img2)

# Disable nonmaxSuppression
fast.setNonmaxSuppression(0)
kp = fast.detect(img, None)

print( "Total Keypoints without nonmaxSuppression: {}".format(len(kp)) )

img3 = cv.drawKeypoints(img, kp, None, color=(255,0,0))

cv.imwrite('fast_false.png', img3)
```

See the results. The first image shows FAST with nonmaxSuppression, and the second one without nonmaxSuppression:

![image](https://docs.opencv.org/4.x/fast_kp.jpg)

<details>
<summary>Click here to know more</summary>

```python
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt

# Load the image in grayscale
img = cv.imread('images/chess.png', cv.IMREAD_GRAYSCALE)

# Check if the image was successfully loaded
if img is None:
    print("Error: Image not found or unable to load.")
else:
    print("Image loaded successfully.")

    # Initiate FAST object with default values
    if hasattr(cv, 'FastFeatureDetector_create'):
        fast = cv.FastFeatureDetector_create()

        # Find and draw the keypoints
        kp = fast.detect(img, None)
        img2 = cv.drawKeypoints(img, kp, None, color=(255, 0, 0))

        # Print all default params
        print("Threshold: {}".format(fast.getThreshold()))
        print("nonmaxSuppression: {}".format(fast.getNonmaxSuppression()))
        print("neighborhood: {}".format(fast.getType()))
        print("Total Keypoints with nonmaxSuppression: {}".format(len(kp)))

        # Display the image with keypoints
        cv.imshow('FAST with nonmaxSuppression', img2)

        # Disable nonmaxSuppression
        fast.setNonmaxSuppression(False)
        kp = fast.detect(img, None)

        print("Total Keypoints without nonmaxSuppression: {}".format(len(kp)))

        img3 = cv.drawKeypoints(img, kp, None, color=(255, 0, 0))

        # Display the image without nonmaxSuppression
        cv.imshow('FAST without nonmaxSuppression', img3)

        # Wait for a key press and close windows
        cv.waitKey(0)
        cv.destroyAllWindows()
    else:
        print("Error: FastFeatureDetector_create is not available in your OpenCV installation.")
```

### Running the Script

1. **Image Load Check:**

   - If the image doesn't load (`img is None`), double-check the path or use an absolute path to the image file.

2. **OpenCV Functionality Check:**

   - If `FastFeatureDetector_create()` is not available, ensure you have the correct OpenCV package installed. If the issue persists, you may need to install OpenCV via `conda` if you are using a Conda environment.

3. **Displaying Images:**
   - The code uses `cv.imshow()` to display images. Ensure your environment supports GUI windows. If you're running this script in a headless environment (e.g., via SSH without X11 forwarding), `cv.imshow()` will not work, and you'll need to save the images to files using `cv.imwrite()` and then view them manually.

By following these steps and addressing any errors from the debug statements, you should be able to identify and fix the issues, ensuring the script runs successfully.

</details>
<details>
   <summary>FAST feature detection on a video</summary>
   To implement FAST feature detection on a video, you'll need to modify the code to process video frames instead of a single image. Here’s how you can adapt the code for video processing:

1. **Read the video**: Use `cv.VideoCapture` to load the video file.
2. **Process each frame**: Apply the FAST detector to each frame.
3. **Save or display the processed frames**: You can either display the frames in a window or save them to a new video file.

Here's the modified code for video processing:

```python
import numpy as np
import cv2 as cv

cap = cv.VideoCapture(0)

# Check if video opened successfully
if not cap.isOpened():
    print("Error opening video file")
    exit()

# Create a VideoWriter object to save the processed video
fourcc = cv.VideoWriter_fourcc(*'XVID')
out = cv.VideoWriter('processed_video.avi', fourcc, 20.0, (int(cap.get(3)), int(cap.get(4))))

# Initiate FAST object with default values
fast = cv.FastFeatureDetector_create()

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break

    # Convert frame to grayscale
    gray = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)

    # Find keypoints
    kp = fast.detect(gray, None)

    # Draw keypoints
    img_with_keypoints = cv.drawKeypoints(frame, kp, None, color=(255, 0, 0))

    # Write the frame into the output video
    out.write(img_with_keypoints)

    # Display the resulting frame (optional)
    cv.imshow('Frame', img_with_keypoints)
    if cv.waitKey(1) & 0xFF == ord('q'):
        break

# Release everything if job is finished
cap.release()
out.release()
cv.destroyAllWindows()

# Print FAST parameters
print("Threshold: {}".format(fast.getThreshold()))
print("nonmaxSuppression: {}".format(fast.getNonmaxSuppression()))
print("Neighborhood: {}".format(fast.getType()))
print("Total Keypoints with nonmaxSuppression: {}".format(len(kp)))
```

### Key Points:

1. **Video Capture and Write**: `cv.VideoCapture` reads the video, and `cv.VideoWriter` saves the processed video frames.
2. **Processing Frames**: Each frame is converted to grayscale, keypoints are detected, and keypoints are drawn on the frame.
3. **Display and Save**: Frames are displayed (optional) and saved to a new video file.

### Things to Replace:

- **`your_video.mp4`**: Replace with the path to your video file.
- **`processed_video.avi`**: The output video file path. You can change the format if needed (e.g., `.mp4`).

Make sure you have the necessary codecs installed to handle video files and the `opencv-python` package is installed in your environment.

</details>

## Additional Resources

- Edward Rosten and Tom Drummond, "Machine learning for high-speed corner detection" in 9th European Conference on Computer Vision, vol. 1, 2006, pp. 430–443.
- Edward Rosten, Reid Porter, and Tom Drummond, "Faster and better: a machine learning approach to corner detection" in IEEE Trans. Pattern Analysis and Machine Intelligence, 2010, vol 32, pp. 105-119.

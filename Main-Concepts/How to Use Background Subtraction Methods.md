# How to Use Background Subtraction Methods


Background subtraction (BS) is a common and widely used technique for generating a foreground mask (namely, a binary image containing the pixels belonging to moving objects in the scene) by using static cameras. As the name suggests, BS calculates the foreground mask by performing a subtraction between the current frame and a background model, containing the static part of the scene or, more generally, everything that can be considered as background given the characteristics of the observed scene.

Background modeling consists of two main steps:
1. Background Initialization;
2. Background Update.

In the first step, an initial model of the background is computed, while in the second step, that model is updated to adapt to possible changes in the scene.

In this tutorial, we will learn how to perform BS using OpenCV.

## Goals
In this tutorial, you will learn how to:
- Read data from videos or image sequences using `cv::VideoCapture`;
- Create and update the background model using `cv::BackgroundSubtractor` class;
- Get and show the foreground mask using `cv::imshow`.

## Code
Below you can find the source code. We will let the user choose to process either a video file or a sequence of images.

We will use `cv::BackgroundSubtractorMOG2` in this sample to generate the foreground mask.

The results, as well as the input data, are shown on the screen.

**Downloadable code:** [Click here]

### Code at a Glance:
```python
from __future__ import print_function
import cv2 as cv
import argparse

parser = argparse.ArgumentParser(description='This program shows how to use background subtraction methods provided by OpenCV. You can process both videos and images.')
parser.add_argument('--input', type=str, help='Path to a video or a sequence of images.', default='vtest.avi')
parser.add_argument('--algo', type=str, help='Background subtraction method (KNN, MOG2).', default='MOG2')
args = parser.parse_args()

if args.algo == 'MOG2':
    backSub = cv.createBackgroundSubtractorMOG2()
else:
    backSub = cv.createBackgroundSubtractorKNN()

capture = cv.VideoCapture(cv.samples.findFileOrKeep(args.input))
if not capture.isOpened():
    print('Unable to open: ' + args.input)
    exit(0)

while True:
    ret, frame = capture.read()
    if frame is None:
        break

    fgMask = backSub.apply(frame)

    cv.rectangle(frame, (10, 2), (100, 20), (255, 255, 255), -1)
    cv.putText(frame, str(capture.get(cv.CAP_PROP_POS_FRAMES)), (15, 15),
               cv.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 0))

    cv.imshow('Frame', frame)
    cv.imshow('FG Mask', fgMask)

    keyboard = cv.waitKey(30)
    if keyboard == 'q' or keyboard == 27:
        break
```
<details>
<summary>Click here to see the code explanation</summary>

### 1. Importing Modules
```python
from __future__ import print_function
import cv2 as cv
import argparse
```
- **`from __future__ import print_function`**: Ensures compatibility between Python 2 and Python 3 for the `print` function. This allows the use of the `print()` function even if the code is executed in Python 2.
- **`import cv2 as cv`**: Imports the OpenCV library, which provides functions for image processing and computer vision.
- **`import argparse`**: Imports the `argparse` module, which is used to handle command-line arguments.

### 2. Argument Parsing
```python
parser = argparse.ArgumentParser(description='This program shows how to use background subtraction methods provided by OpenCV. You can process both videos and images.')
parser.add_argument('--input', type=str, help='Path to a video or a sequence of images.', default='vtest.avi')
parser.add_argument('--algo', type=str, help='Background subtraction method (KNN, MOG2).', default='MOG2')
args = parser.parse_args()
```
- **`argparse.ArgumentParser()`**: Creates a parser object to handle command-line arguments. The `description` parameter provides a brief description of the program.
- **`parser.add_argument('--input', ...)`**: Adds an argument `--input` to specify the path to the input video or image sequence. If not provided, it defaults to `'vtest.avi'`.
- **`parser.add_argument('--algo', ...)`**: Adds an argument `--algo` to choose the background subtraction algorithm, either `'KNN'` or `'MOG2'`. It defaults to `'MOG2'`.
- **`args = parser.parse_args()`**: Parses the command-line arguments and stores them in `args`.

### 3. Selecting the Background Subtraction Algorithm
```python
if args.algo == 'MOG2':
    backSub = cv.createBackgroundSubtractorMOG2()
else:
    backSub = cv.createBackgroundSubtractorKNN()
```
- **`if args.algo == 'MOG2':`**: Checks if the user selected the `MOG2` algorithm.
- **`backSub = cv.createBackgroundSubtractorMOG2()`**: Creates a background subtractor using the MOG2 algorithm. MOG2 is a Gaussian Mixture-based Background/Foreground Segmentation Algorithm.
- **`else: backSub = cv.createBackgroundSubtractorKNN()`**: If the algorithm is not `MOG2`, the KNN (K-Nearest Neighbors) algorithm is used instead for background subtraction.

### 4. Capturing the Video or Images
```python
capture = cv.VideoCapture(cv.samples.findFileOrKeep(args.input))
if not capture.isOpened():
    print('Unable to open: ' + args.input)
    exit(0)
```
- **`capture = cv.VideoCapture(cv.samples.findFileOrKeep(args.input))`**: Opens the video file or image sequence specified by the `--input` argument. `cv.samples.findFileOrKeep()` helps locate the file.
- **`if not capture.isOpened():`**: Checks if the video file was successfully opened.
- **`print('Unable to open: ' + args.input)`**: Prints an error message if the file cannot be opened.
- **`exit(0)`**: Exits the program if the file cannot be opened.

### 5. Processing the Video Frames
```python
while True:
    ret, frame = capture.read()
    if frame is None:
        break

    fgMask = backSub.apply(frame)

    cv.rectangle(frame, (10, 2), (100, 20), (255, 255, 255), -1)
    cv.putText(frame, str(capture.get(cv.CAP_PROP_POS_FRAMES)), (15, 15),
               cv.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 0))

    cv.imshow('Frame', frame)
    cv.imshow('FG Mask', fgMask)

    keyboard = cv.waitKey(30)
    if keyboard == 'q' or keyboard == 27:
        break
```
- **`while True:`**: Creates an infinite loop to continuously read frames from the video or image sequence.
- **`ret, frame = capture.read()`**: Reads the next frame from the video. `ret` is a boolean indicating if the frame was successfully read. `frame` is the actual image array.
- **`if frame is None:`**: Checks if the frame is empty or if the end of the video is reached.
- **`break`**: Exits the loop if no more frames are available.

#### Background Subtraction
```python
fgMask = backSub.apply(frame)
```
- **`fgMask = backSub.apply(frame)`**: Applies the selected background subtraction method to the current frame to generate a foreground mask (`fgMask`). This mask highlights the moving objects in the scene.

#### Drawing and Displaying the Frame
```python
cv.rectangle(frame, (10, 2), (100, 20), (255, 255, 255), -1)
cv.putText(frame, str(capture.get(cv.CAP_PROP_POS_FRAMES)), (15, 15),
           cv.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 0))
```
- **`cv.rectangle(frame, (10, 2), (100, 20), (255, 255, 255), -1)`**: Draws a white rectangle on the frame. This rectangle is positioned at the top left corner and is used to overlay text.
- **`cv.putText(frame, str(capture.get(cv.CAP_PROP_POS_FRAMES)), (15, 15), cv.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 0))`**: Writes the current frame number inside the rectangle using the `cv.putText()` function.

#### Displaying the Results
```python
cv.imshow('Frame', frame)
cv.imshow('FG Mask', fgMask)
```
- **`cv.imshow('Frame', frame)`**: Displays the original frame with the overlaid text.
- **`cv.imshow('FG Mask', fgMask)`**: Displays the foreground mask, showing the detected moving objects.

#### Handling User Input
```python
keyboard = cv.waitKey(30)
if keyboard == 'q' or keyboard == 27:
    break
```
- **`keyboard = cv.waitKey(30)`**: Waits for a key press for 30 milliseconds. The function returns the ASCII value of the pressed key.
- **`if keyboard == 'q' or keyboard == 27:`**: If the 'q' key (or ESC key) is pressed, the loop breaks, and the program exits.

### Summary
This program uses OpenCV to perform background subtraction on a video or sequence of images. It allows the user to choose between two algorithms: MOG2 and KNN. The program processes each frame, applies the chosen background subtraction method, and displays both the original frame and the foreground mask. The user can exit the program by pressing 'q' or the ESC key.
</details>

<details>
<summary>Click here to see the usage</summary>
  To use your camera for background subtraction (BS) with the code provided earlier, you can modify the `input` argument to use the camera feed instead of a video file. Here's how you can do it:

### Modified Code for Camera Input with Background Subtraction:

```python
from __future__ import print_function
import cv2 as cv
import argparse

# Setting up argument parser
parser = argparse.ArgumentParser(description='This program shows how to use background subtraction methods provided by OpenCV. You can process both videos and images.')
parser.add_argument('--algo', type=str, help='Background subtraction method (KNN, MOG2).', default='MOG2')
args = parser.parse_args()

# Select background subtraction method
if args.algo == 'MOG2':
    backSub = cv.createBackgroundSubtractorMOG2()
else:
    backSub = cv.createBackgroundSubtractorKNN()

# Capture video from the default camera (0)
capture = cv.VideoCapture(0)

if not capture.isOpened():
    print('Error: Unable to open the camera.')
    exit(0)

while True:
    ret, frame = capture.read()
    if not ret:
        print("Error: Unable to read the frame.")
        break

    # Apply the background subtraction algorithm to the frame
    fgMask = backSub.apply(frame)

    # Add text on the frame showing the current frame number
    cv.rectangle(frame, (10, 2), (100, 20), (255, 255, 255), -1)
    cv.putText(frame, str(capture.get(cv.CAP_PROP_POS_FRAMES)), (15, 15),
               cv.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 0))

    # Display the original frame and the foreground mask
    cv.imshow('Frame', frame)
    cv.imshow('FG Mask', fgMask)

    # Exit the loop when 'q' or ESC is pressed
    keyboard = cv.waitKey(30)
    if keyboard == 'q' or keyboard == 27:
        break

# Release the camera and close all OpenCV windows
capture.release()
cv.destroyAllWindows()
```

### Explanation of Changes:
1. **Camera as Input**:
   - `capture = cv.VideoCapture(0)` is used to capture video from your default camera instead of reading from a file.
   
2. **Frame Handling**:
   - The rest of the code remains largely the same, where each frame captured from the camera is processed for background subtraction using either the MOG2 or KNN algorithm.
  
3. **Foreground Mask**:
   - The foreground mask `fgMask` is calculated for each frame using `backSub.apply(frame)`.

4. **Display Windows**:
   - Two windows are displayed: one showing the original frame (`Frame`) and another showing the foreground mask (`FG Mask`).

5. **Loop Exit**:
   - The loop will continue capturing and processing frames until the user presses 'q' or ESC.

This setup will allow you to perform background subtraction using your webcam in real time.
</details>

## Results
With the `vtest.avi` video, for the following frame:

The output of the program will look as follows for the MOG2 method (gray areas are detected shadows):

![MOG2 Output](https://docs.opencv.org/5.x/Background_Subtraction_Tutorial_frame.jpg)

The output of the program will look as follows for the KNN method (gray areas are detected shadows):

![KNN Output](https://docs.opencv.org/5.x/Background_Subtraction_Tutorial_result_MOG2.jpg)

## References
- [Background Models Challenge (BMC) website](http://bmc.iut-auvergne.com/)
- [A Benchmark Dataset for Foreground/Background Extraction](https://www.researchgate.net/publication/260091324_A_Benchmark_Dataset_for_ForegroundBackground_Extraction)

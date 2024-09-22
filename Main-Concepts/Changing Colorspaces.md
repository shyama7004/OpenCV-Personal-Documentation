# OpenCV Color Space Conversion and Object Tracking

## Goal
In this tutorial, you will learn how to:
- Convert images from one color-space to another, like BGR to Gray, BGR to HSV, etc.
- Create an application to extract a colored object in a video.

You will learn the following functions: `cv.cvtColor()`, `cv.inRange()`, etc.

## Changing Color-space
There are more than 150 color-space conversion methods available in OpenCV. We will look into only two, which are most widely used: BGR to Gray and BGR to HSV.

For color conversion, we use the function `cv.cvtColor(input_image, flag)` where `flag` determines the type of conversion.

For BGR to Gray conversion, we use the flag `cv.COLOR_BGR2GRAY`. Similarly, for BGR to HSV, we use the flag `cv.COLOR_BGR2HSV`. To get other flags, run the following commands in your Python terminal:

```python
>>> import cv2 as cv
>>> flags = [i for i in dir(cv) if i.startswith('COLOR_')]
>>> print(flags)
```
### Example Code :
```py
import numpy as np
import cv2 as cv

img1 = cv.imread("messi.webp") 
assert img1 is not None,"file couldn't be read ,check with os.path.exists()"

conversion = cv.cvtColor(img1 ,cv.COLOR_BGR2GRAY)
cv.imshow("Messi" , conversion)
cv.waitKey(0)
```

<div align="center"><h2><strong>Result</strong></h2></div>

<div align="cener"><img src="https://private-user-images.githubusercontent.com/85214856/354307888-7ccd4839-838a-4371-83f5-c6f392061cd6.jpeg?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjI1MzA3NDYsIm5iZiI6MTcyMjUzMDQ0NiwicGF0aCI6Ii84NTIxNDg1Ni8zNTQzMDc4ODgtN2NjZDQ4MzktODM4YS00MzcxLTgzZjUtYzZmMzkyMDYxY2Q2LmpwZWc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwODAxJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDgwMVQxNjQwNDZaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT01NzQwZjEwNjMzMjY1OGU1Mjk3Zjk2OTRhNWRiOTAzZDZiZDRlOWNkY2I5Njg2ZmQ2Y2E0M2NkZWI0YWU1M2Y5JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.k52-VV-AZ86FtwUx7m9nZMSX3o4cB0DIx_-RSPqWI8o" ></div>

### Note
For HSV, hue range is `[0,179]`, saturation range is `[0,255]`, and value range is `[0,255]`. `Different software uses different scales. So if you are comparing OpenCV values with them, you need to normalize these ranges`.

## Object Tracking
Now that we know how to convert a BGR image to HSV, we can use this to extract a colored object. In HSV, it is easier to represent a color than in BGR color-space. In our application, we will try to extract a blue-colored object. Here is the method:

1. Take each frame of the video.
2. Convert from BGR to HSV color-space.
3. Threshold the HSV image for a range of blue color.
4. Extract the blue object alone; we can do whatever we want on that image.

Below is the code, which is commented in detail:

```python
import cv2 as cv
import numpy as np

cap = cv.VideoCapture(0)

while(1):

    # Take each frame
    _, frame = cap.read()

    # Convert BGR to HSV
    hsv = cv.cvtColor(frame, cv.COLOR_BGR2HSV)

    # Define range of blue color in HSV
    lower_blue = np.array([110, 50, 50])
    upper_blue = np.array([130, 255, 255])

    # Threshold the HSV image to get only blue colors
    mask = cv.inRange(hsv, lower_blue, upper_blue)

    # Bitwise-AND mask and original image
    res = cv.bitwise_and(frame, frame, mask=mask)

    cv.imshow('frame', frame)
    cv.imshow('mask', mask)
    cv.imshow('res', res)
    k = cv.waitKey(5) & 0xFF
    if k == 27:
        break

cv.destroyAllWindows()
```
<details>
    <summary>Click here to see the C++ code</summary>

```cpp
#include <opencv2/opencv.hpp>

int main() {
    // Open the default camera
    cv::VideoCapture cap(0);

    // Check if camera opened successfully
    if (!cap.isOpened()) {
        std::cout << "Error: Could not open camera" << std::endl;
        return -1;
    }

    while (true) {
        cv::Mat frame;
        
        // Capture frame-by-frame
        cap >> frame;

        // If the frame is empty, break immediately
        if (frame.empty()) break;

        // Convert from BGR to HSV
        cv::Mat hsv;
        cv::cvtColor(frame, hsv, cv::COLOR_BGR2HSV);

        // Define the range of blue color in HSV
        cv::Scalar lower_blue(110, 50, 50);
        cv::Scalar upper_blue(130, 255, 255);

        // Threshold the HSV image to get only blue colors
        cv::Mat mask;
        cv::inRange(hsv, lower_blue, upper_blue, mask);

        // Bitwise-AND mask and original image
        cv::Mat res;
        cv::bitwise_and(frame, frame, res, mask);

        // Display the resulting frames
        cv::imshow("frame", frame);
        cv::imshow("mask", mask);
        cv::imshow("res", res);

        // Wait for 'Esc' key press for 5 ms. If 'Esc' is pressed, break the loop
        if (cv::waitKey(5) == 27) break;
    }

    // Release the camera and destroy all windows
    cap.release();
    cv::destroyAllWindows();

    return 0;
}
```
</details>

Below image shows tracking of the blue object:

![Tracking Blue Object](https://docs.opencv.org/4.x/frame.jpg)<br>
<details>
<summary>Python Code explanation</summary>
    
`Explanation in detail`

This code captures video from your webcam and processes each frame to detect blue objects. Here’s a step-by-step explanation of the code:

1. **Import Libraries:**
   ```python
   import cv2 as cv
   import numpy as np
   ```
   - `cv2`: OpenCV library for computer vision tasks.
   - `numpy`: Library for numerical operations in Python.

2. **Capture Video:**
   ```python
   cap = cv.VideoCapture(0)
   ```
   - `cv.VideoCapture(0)`: Initializes video capture with the default camera (usually the webcam).

3. **Process Each Frame in a Loop:**
   ```python
   while(1):
   ```

4. **Capture Frame:**
   ```python
   _, frame = cap.read()
   ```
   - `cap.read()`: Reads a frame from the video capture.
   - `_` (underscore): Ignored value, since `cap.read()` returns two values (ret, frame) but we only need the frame.

5. **Convert Frame from BGR to HSV:**
   ```python
   hsv = cv.cvtColor(frame, cv.COLOR_BGR2HSV)
   ```
   - `cv.cvtColor(frame, cv.COLOR_BGR2HSV)`: Converts the frame from BGR color space to HSV color space. HSV (Hue, Saturation, Value) is better for color detection.

6. **Define Range for Blue Color in HSV:**
   ```python
   lower_blue = np.array([110, 50, 50])
   upper_blue = np.array([130, 255, 255])
   ```
   - `np.array([110, 50, 50])`: Lower bound of blue color in HSV.
   - `np.array([130, 255, 255])`: Upper bound of blue color in HSV.

7. **Threshold the HSV Image to Get Only Blue Colors:**
   ```python
   mask = cv.inRange(hsv, lower_blue, upper_blue)
   ```
   - `cv.inRange(hsv, lower_blue, upper_blue)`: Creates a mask where blue colors are white (255) and other colors are black (0).

8. **Bitwise-AND Mask and Original Image:**
   ```python
   res = cv.bitwise_and(frame, frame, mask=mask)
   ```
   - `cv.bitwise_and(frame, frame, mask=mask)`: Applies the mask to the original frame, keeping only the blue regions.

For more details click on : [Argument explanation](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/21.1.md)

9. **Display Frames:**
   ```python
   cv.imshow('frame', frame)
   cv.imshow('mask', mask)
   cv.imshow('res', res)
   ```
   - `cv.imshow('frame', frame)`: Displays the original frame.
   - `cv.imshow('mask', mask)`: Displays the mask.
   - `cv.imshow('res', res)`: Displays the result of bitwise AND operation.

10. **Exit on 'Esc' Key Press:**
    ```python
    k = cv.waitKey(5) & 0xFF
    if k == 27:
        break
    ```
    - `cv.waitKey(5)`: Waits for a key event for 5 milliseconds.
    - `k == 27`: Checks if the 'Esc' key (ASCII 27) is pressed. If so, breaks the loop.
    - `& 0xFF`: This is a `bitwise AND` operation with the hexadecimal value 0xFF (binary 11111111). In Python, hexadecimal values are prefixed with `0x`.

`0xFF` is a mask that selects the last `8 bits` (rightmost byte) of the integer value returned by cv2.waitKey(). This is done to ensure platform-independent key press detection.

11. **Release Resources:**
    ```python
    cv.destroyAllWindows()
    ```
    - `cv.destroyAllWindows()`: Closes all OpenCV windows.

In summary, this code captures video from the webcam, processes each frame to detect blue objects using HSV color space, and displays the original frame, the mask, and the result where only blue regions are visible. The loop continues until the 'Esc' key is pressed.
</details>

> Warning :If your webcam is not working checkout the following things :<br>
> 1. A camera is present on the first hand.<br>
> 2. Permission is granted.<br>
> 3. cv.VideoCapture(0),change the value to 1 or 2.<br>
### Note
There is some noise in the image. We will see how to remove it in later chapters. This is the simplest method in object tracking. Once you learn functions of contours, you can do plenty of things like find the centroid of an object and use it to track the object, draw diagrams just by moving your hand in front of a camera, and other fun stuff.

## How to Find HSV Values to Track?
This is a common question found in stackoverflow.com. It is very simple, and you can use the same function, `cv.cvtColor()`. Instead of passing an image, you just pass the BGR values you want. For example, to find the HSV value of Green, try the following commands in a Python terminal:

```python
>>> green = np.uint8([[[0, 255, 0]]])
>>> hsv_green = cv.cvtColor(green, cv.COLOR_BGR2HSV)
>>> print(hsv_green)
[[[ 60 255 255]]]
```

Now you take `[H-10, 100, 100]` and `[H+10, 255, 255]` as the lower bound and upper bound respectively. Apart from this method, you can use any image editing tools like GIMP or any online converters to find these values, but don't forget to adjust the HSV ranges.

## Additional Resources
### Exercises
Try to find a way to extract more than one colored object, for example, extract red, blue, and green objects simultaneously.

Answer by `chat gpt`

```py
import cv2 as cv
import numpy as np

# Define the HSV ranges for the colors
# Note: Red color has two ranges in HSV
lower_red1 = np.array([0, 100, 100])
upper_red1 = np.array([10, 255, 255])
lower_red2 = np.array([160, 100, 100])
upper_red2 = np.array([179, 255, 255])

lower_blue = np.array([110, 50, 50])
upper_blue = np.array([130, 255, 255])

lower_green = np.array([40, 100, 100])
upper_green = np.array([80, 255, 255])

# Attempt to open the camera
cap = cv.VideoCapture(0)  # Change the index if needed
if not cap.isOpened():
    print("Cannot open camera")
    exit()

while True:
    # Capture frame-by-frame
    ret, frame = cap.read()
    if not ret:
        print("Can't receive frame (stream end?). Exiting ...")
        break

    # Convert BGR to HSV
    hsv = cv.cvtColor(frame, cv.COLOR_BGR2HSV)

    # Create masks for red, blue, and green
    mask_red1 = cv.inRange(hsv, lower_red1, upper_red1)
    mask_red2 = cv.inRange(hsv, lower_red2, upper_red2)
    mask_red = cv.bitwise_or(mask_red1, mask_red2)

    mask_blue = cv.inRange(hsv, lower_blue, upper_blue)
    mask_green = cv.inRange(hsv, lower_green, upper_green)

    # Combine the masks
    combined_mask = cv.bitwise_or(mask_red, mask_blue)
    combined_mask = cv.bitwise_or(combined_mask, mask_green)

    # Bitwise-AND mask and original image
    res = cv.bitwise_and(frame, frame, mask=combined_mask)

    # Display the resulting frames
    cv.imshow('Original Frame', frame)
    cv.imshow('Combined Mask', combined_mask)
    cv.imshow('Result', res)

    # Exit on 'ESC' key press
    if cv.waitKey(5) & 0xFF == 27:
        break

# Release the capture and close windows
cap.release()
cv.destroyAllWindows()
```
<details>
<summary>Click here to see the C++ code</summary>

```cpp
#include <opencv2/opencv.hpp>
#include <iostream>

int main() {
    // Open the camera (index 1)
    cv::VideoCapture cap(1);
    if (!cap.isOpened()) {
        std::cout << "Bahiya camera kam nhi kar rha" << std::endl;
        return -1;
    }

    while (true) {
        cv::Mat frame;
        bool ret = cap.read(frame);
        if (!ret) {
            std::cout << "Bhaiya mai pagal ho chuka" << std::endl;
            break;
        }

        // Convert from BGR to HSV
        cv::Mat hsv;
        cv::cvtColor(frame, hsv, cv::COLOR_BGR2HSV);

        // Define the range of red color in HSV
        cv::Scalar lower_red1(0, 100, 100);
        cv::Scalar upper_red1(10, 255, 255);
        cv::Scalar lower_red2(160, 100, 100);
        cv::Scalar upper_red2(179, 255, 255);

        // Define the range of blue color in HSV
        cv::Scalar lower_blue(110, 50, 50);
        cv::Scalar upper_blue(130, 255, 255);

        // Define the range of green color in HSV
        cv::Scalar lower_green(40, 100, 100);
        cv::Scalar upper_green(80, 255, 255);

        // Create masks for red, blue, and green colors
        cv::Mat mask_red1, mask_red2, mask_red, mask_blue, mask_green;
        cv::inRange(hsv, lower_red1, upper_red1, mask_red1);
        cv::inRange(hsv, lower_red2, upper_red2, mask_red2);
        cv::bitwise_or(mask_red1, mask_red2, mask_red);

        cv::inRange(hsv, lower_blue, upper_blue, mask_blue);
        cv::inRange(hsv, lower_green, upper_green, mask_green);

        // Combine the masks
        cv::Mat combined_mask;
        cv::bitwise_or(mask_red, mask_blue, combined_mask);
        cv::bitwise_or(combined_mask, mask_green, combined_mask);

        // Bitwise-AND mask and original image
        cv::Mat res;
        cv::bitwise_and(frame, frame, res, combined_mask);

        // Display the results
        cv::imshow("frame", frame);
        cv::imshow("mask", combined_mask);
        cv::imshow("res", res);

        // Exit on 'Esc' key press
        if (cv::waitKey(5) == 27) break;
    }

    // Release the camera and destroy all windows
    cap.release();
    cv::destroyAllWindows();

    return 0;
}
```
</details>

---
```
Made by shyama7004 with the help of OpenCV Docs

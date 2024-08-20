# Arithmetic Operations on Images with OpenCV

## Goal
- Learn several arithmetic operations on images, like addition, subtraction, bitwise operations, and more.
- Learn the following functions: `cv.add()`, `cv.addWeighted()`, etc.

## Image Addition
You can add two images with the OpenCV function, `cv.add()`, or simply by the numpy operation `res = img1 + img2`. Both images should be of the same depth and type, or the second image can just be a scalar value.

### Note
There is a difference between OpenCV addition and Numpy addition. OpenCV addition is a saturated operation while Numpy addition is a modulo operation.

For more explanation on [Modulo v/s Saturated Operation](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/2.3.md)

For example, consider the below sample:

```python
>>> x = np.uint8([250])
>>> y = np.uint8([10])

>>> print(cv.add(x, y))  # 250+10 = 260 => 255
[[255]]

>>> print(x + y)         # 250+10 = 260 % 256 = 4
[4]
```

This will be more visible when you add two images. Stick with OpenCV functions, because they will provide a better result.

## Image Blending
This is also image addition, but different weights are given to images in order to give a feeling of blending or transparency. Images are added as per the equation below:

<div align ="center"><img src ="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/6.png" width="500" height ="100"></div>

By varying &alpha; from 0 to 1, you can perform a cool transition between one image to another.

Here I took two images to blend together. The first image is given a weight of 0.7 and the second image is given 0.3. `cv.addWeighted()` applies the following equation to the image:

<div align ="center"><img src ="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/7.png" width="500" height ="100"></div>


Here &gamma; is taken as zero.

```python
img1 = cv.imread('ml.png')
img2 = cv.imread('opencv-logo.png')
assert img1 is not None, "file could not be read, check with os.path.exists()"
assert img2 is not None, "file could not be read, check with os.path.exists()"

dst = cv.addWeighted(img1, 0.7, img2, 0.3, 0)

cv.imshow('dst', dst)
cv.waitKey(0)
cv.destroyAllWindows()
```
<details>
  <summary>Click to see the C++ code!</summary>
   
   ```Cpp
#include<iostream>
#include<opencv2/opencv.hpp>

using namespace std;
using namespace cv;

int main()
{
    // Read the images
    Mat img1 = imread("images/12.jpg");
    Mat img2 = imread("images/13.jpg");

    // Check if the images are loaded successfully
    if (img1.empty() || img2.empty())
    {
        cout << "Could not open or find the images!" << endl;
        return -1;
    }

    // Blend the images
    Mat blend;
    addWeighted(img1, 0.7, img2, 0.3, 0.0, blend);

    // Display the blended image
    imshow("Blended Image", blend);

    // Wait until user presses a key (5 ms delay)
    if (waitKey(0) == 27) // 27 is the ASCII code for the ESC key
    {
        destroyAllWindows();
        return 0;
    }

    return 0;
}
```
</details>


Check the result below:

![Blended Image](https://docs.opencv.org/4.x/blending.jpg)

## Bitwise Operations
This includes the bitwise AND, OR, NOT, and XOR operations. They are highly useful while extracting any part of the image, defining and working with non-rectangular ROI's, and more. Below we will see an example of how to change a particular region of an image.

I want to put the OpenCV logo above an image. If I add two images, it will change the color. If I blend them, I get a transparent effect. But I want it to be opaque. If it was a rectangular region, I could use ROI as we did in the last chapter. But the OpenCV logo is not a rectangular shape. So you can do it with bitwise operations as shown below:

```python
# Load two images
img1 = cv.imread('messi5.jpg')
img2 = cv.imread('opencv-logo-white.png')
assert img1 is not None, "file could not be read, check with os.path.exists()"
assert img2 is not None, "file could not be read, check with os.path.exists()"

# I want to put logo on top-left corner, So I create a ROI
rows, cols, channels = img2.shape
roi = img1[0:rows, 0:cols]

# Now create a mask of logo and create its inverse mask also
img2gray = cv.cvtColor(img2, cv.COLOR_BGR2GRAY)
```python
ret, mask = cv.threshold(img2gray, 10, 255, cv.THRESH_BINARY)
mask_inv = cv.bitwise_not(mask)

# Now black-out the area of logo in ROI
img1_bg = cv.bitwise_and(roi, roi, mask=mask_inv)

# Take only region of logo from logo image
img2_fg = cv.bitwise_and(img2, img2, mask=mask)

# Put logo in ROI and modify the main image
dst = cv.add(img1_bg, img2_fg)
img1[0:rows, 0:cols] = dst

cv.imshow('res', img1)
cv.waitKey(0)
cv.destroyAllWindows()
```
<details>
   <summary>Click to see the detailed Explanation</summary>

### Explanation

#### 1. Loading Images
```python
import cv2 as cv

# Load two images
img1 = cv.imread('messi5.jpg')
img2 = cv.imread('opencv-logo-white.png')
assert img1 is not None, "file could not be read, check with os.path.exists()"
assert img2 is not None, "file could not be read, check with os.path.exists()"
```
- **Purpose**: This section loads two images using OpenCV's `imread` function.
- **`img1`**: This is the main image (e.g., 'messi5.jpg').
- **`img2`**: This is the logo image you want to overlay on the main image (e.g., 'opencv-logo-white.png').
- **Assertions**: The `assert` statements ensure that the images are loaded correctly. If an image fails to load, it raises an assertion error with a message.

#### 2. Creating a Region of Interest (ROI)
```python
# I want to put the logo on the top-left corner, so I create an ROI
rows, cols, channels = img2.shape
roi = img1[0:rows, 0:cols]
```
- **Purpose**: This section sets up a region in `img1` where the logo will be placed.
- **`img2.shape`**: Retrieves the dimensions of the logo image. `rows` and `cols` are the height and width, respectively, and `channels` is the number of color channels.
- **`roi`**: Defines a region of interest in `img1` that matches the size of `img2`. This region is located at the top-left corner of `img1`.

#### 3. Creating Masks
```python
# Now create a mask of the logo and create its inverse mask also
img2gray = cv.cvtColor(img2, cv.COLOR_BGR2GRAY)
ret, mask = cv.threshold(img2gray, 10, 255, cv.THRESH_BINARY)
mask_inv = cv.bitwise_not(mask)
```
- **`cv.cvtColor`**: Converts the logo image (`img2`) to grayscale, resulting in `img2gray`.
- **`cv.threshold`**: Applies a binary threshold to the grayscale logo image. This creates a binary mask (`mask`) where the logo is white (255) and the background is black (0).
- **`cv.bitwise_not`**: Creates an inverse of the mask (`mask_inv`), where the logo is black (0) and the background is white (255).

#### 4. Blacking Out the Logo Area in ROI
```python
# Now black-out the area of the logo in ROI
img1_bg = cv.bitwise_and(roi, roi, mask=mask_inv)
```
- **Purpose**: This section removes the area of the logo from the ROI in `img1`.
- **`cv.bitwise_and`**: Applies a bitwise AND operation between the ROI and itself, but only where `mask_inv` is white (255). This zeroes out the area where the logo will go, effectively blacking it out.

#### 5. Extracting the Logo Region
```python
# Take only region of the logo from the logo image
img2_fg = cv.bitwise_and(img2, img2, mask=mask)
```
- **Purpose**: This section extracts just the logo from `img2`.
- **`cv.bitwise_and`**: Applies a bitwise AND operation between `img2` and itself, but only where `mask` is white (255). This isolates the logo from its background.

#### 6. Combining the Logo with the ROI
```python
# Put the logo in ROI and modify the main image
dst = cv.add(img1_bg, img2_fg)
img1[0:rows, 0:cols] = dst
```
- **Purpose**: This section combines the blacked-out ROI with the isolated logo and updates the main image.
- **`cv.add`**: Adds `img1_bg` and `img2_fg` together, placing the logo in the blacked-out area.
- **`img1[0:rows, 0:cols] = dst`**: Replaces the original ROI in `img1` with the combined image (`dst`).

#### 7. Displaying the Result
```python
cv.imshow('res', img1)
cv.waitKey(0)
cv.destroyAllWindows()
```
- **`cv.imshow`**: Displays the modified image (`img1`) in a window titled 'res'.
- **`cv.waitKey(0)`**: Waits indefinitely for a key press. This keeps the window open until a key is pressed.
- **`cv.destroyAllWindows`**: Closes all OpenCV windows.
</details>

![Mask and Result](https://docs.opencv.org/4.x/overlay.jpg)

## Additional Resources
### Exercises
- Create a slideshow of images in a folder with smooth transition between images using the `cv.addWeighted` function.

### Solution
To create a slideshow of images in a folder with smooth transitions using `cv.addWeighted` in OpenCV, you'll need to:

1. Read all images from the specified folder.
2. Display each image for a certain duration.
3. Implement smooth transitions between images using `cv.addWeighted` to blend one image into the next.

Here's a step-by-step guide with the code:

### Step-by-Step Guide

1. **Import necessary libraries:**
   You'll need OpenCV for image processing and `os` for file operations.

2. **Read images from the folder:**
   Use `os.listdir` to get a list of all image files in the folder.

3. **Display each image:**
   Use `cv.imshow` and `cv.waitKey` to display each image for a specific duration.

4. **Smooth transition using `cv.addWeighted`:**
   Implement a loop that blends the current image into the next one using `cv.addWeighted`.

### Code Example

```python
import cv2 as cv
import os

def load_images_from_folder(folder):
    images = []
    for filename in os.listdir(folder):
        img = cv.imread(os.path.join(folder, filename))
        if img is not None:
            images.append(img)
    return images

def show_slideshow(images, transition_time=1, display_time=2):
    num_images = len(images)
    if num_images == 0:
        print("No images to display.")
        return

    for i in range(num_images):
        next_img = images[(i + 1) % num_images]
        current_img = images[i]
        
        # Display the current image
        cv.imshow('Slideshow', current_img)
        cv.waitKey(display_time * 1000)  # Display the image for display_time seconds

        # Transition effect
        for alpha in range(0, 101):
            blend = cv.addWeighted(current_img, (100 - alpha) / 100.0, next_img, alpha / 100.0, 0)
            cv.imshow('Slideshow', blend)
            cv.waitKey(transition_time * 10)  # Control the speed of transition (10 ms per step)

        # Pause briefly before showing the next image
        cv.waitKey(200)

    cv.destroyAllWindows()

# Path to the folder containing images
folder_path = 'images'

# Load images from the folder
images = load_images_from_folder(folder_path)

# Show slideshow with smooth transitions
show_slideshow(images, transition_time=1, display_time=2)
```

<details>
   <summary>Click to see the detailed Explanation</summary>

### Explanation

```python
import cv2 as cv
import os
```
- `import cv2 as cv`: Imports the OpenCV library, which is used for image processing.
- `import os`: Imports the os library, which provides a way to interact with the operating system, such as reading files in a directory.

```python
def load_images_from_folder(folder):
    images = []
    for filename in os.listdir(folder):
        img = cv.imread(os.path.join(folder, filename))
        if img is not None:
            images.append(img)
    return images
```
- `def load_images_from_folder(folder):`: Defines a function named `load_images_from_folder` that takes a folder path as an argument.
- `images = []`: Initializes an empty list to store the images.
- `for filename in os.listdir(folder):`: Iterates through each file in the specified folder.More about [os.listdir](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/2.3.4.md)
- `img = cv.imread(os.path.join(folder, filename))`: Reads the image file using OpenCV's `cv.imread` function. `os.path.join(folder, filename)` constructs the full path to the image file.More about ,[os.path.join](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/2.3.3.md)
- `if img is not None:`: Checks if the image was successfully read (i.e., it's not `None`).
- `images.append(img)`: If the image was successfully read, it is appended to the `images` list.
- `return images`: Returns the list of images.

```python
def show_slideshow(images, transition_time=1, display_time=2):
    num_images = len(images)
    if num_images == 0:
        print("No images to display.")
        return
```
- `def show_slideshow(images, transition_time=1, display_time=2):`: Defines a function named `show_slideshow` that takes a list of images, a transition time, and a display time as arguments.
- `num_images = len(images)`: Stores the number of images in the `num_images` variable.More about [len(images)](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/2.3.5.md)
- `if num_images == 0:`: Checks if there are no images in the list.
- `print("No images to display.")`: Prints a message indicating that there are no images to display.
- `return`: Exits the function early if there are no images.

```python
    for i in range(num_images):
        next_img = images[(i + 1) % num_images]
        current_img = images[i]
```
- `for i in range(num_images):`: Iterates over the indices of the images in the list.
- `next_img = images[(i + 1) % num_images]`: Gets the next image in the list, using modulo to wrap around to the first image if the current image is the last one.More about this code-snippet ,[{images{(i+1) % num_images}](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/2.3.6.md)
- `current_img = images[i]`: Gets the current image in the list.

```python
        cv.imshow('Slideshow', current_img)
        cv.waitKey(display_time * 1000)  # Display the image for display_time seconds
```
- `cv.imshow('Slideshow', current_img)`: Displays the current image in a window titled "Slideshow".
- `cv.waitKey(display_time * 1000)`: Waits for `display_time` seconds (converted to milliseconds) before proceeding. This keeps the current image displayed for the specified duration.

```python
        for alpha in range(0, 101):
            blend = cv.addWeighted(current_img, (100 - alpha) / 100.0, next_img, alpha / 100.0, 0)
            cv.imshow('Slideshow', blend)
            cv.waitKey(transition_time * 10)  # Control the speed of transition (10 ms per step)
```
- `for alpha in range(0, 101):`: Iterates from 0 to 100, which will be used to create the transition effect.
- `blend = cv.addWeighted(current_img, (100 - alpha) / 100.0, next_img, alpha / 100.0, 0)`: Blends the current image and the next image based on the alpha value. As `alpha` increases from 0 to 100, the weight of the current image decreases and the weight of the next image increases, creating a smooth transition.
- `cv.imshow('Slideshow', blend)`: Displays the blended image in the "Slideshow" window.
- `cv.waitKey(transition_time * 10)`: Waits for a short time (10 milliseconds multiplied by `transition_time`) to control the speed of the transition.

```python
        cv.waitKey(200)
```
- `cv.waitKey(200)`: Adds a brief pause (200 milliseconds) before showing the next image to ensure smooth transitions.

```python
    cv.destroyAllWindows()
```
- `cv.destroyAllWindows()`: Closes all OpenCV windows when the slideshow is complete.

```python
folder_path = 'images'
```
- `folder_path = 'images'`: Specifies the path to the folder containing the images.

```python
images = load_images_from_folder(folder_path)
```
- `images = load_images_from_folder(folder_path)`: Calls the `load_images_from_folder` function to load all images from the specified folder and stores them in the `images` list.

```python
show_slideshow(images, transition_time=1, display_time=2)
```
- `show_slideshow(images, transition_time=1, display_time=2)`: Calls the `show_slideshow` function to display the images with smooth transitions, specifying `transition_time` and `display_time` in seconds.

This script will load all images from the specified folder, display them in a slideshow with smooth transitions between each image, and ensure each image is displayed for the specified duration.


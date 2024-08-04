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

![Mask and Result](https://docs.opencv.org/4.x/overlay.jpg)

## Additional Resources
### Exercises
- Create a slideshow of images in a folder with smooth transition between images using the `cv.addWeighted` function.

---

This completes the Markdown file with the detailed explanation of arithmetic operations on images using OpenCV.

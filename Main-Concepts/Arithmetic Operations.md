# Arithmetic Operations on Images with OpenCV

## Goal
- Learn several arithmetic operations on images, like addition, subtraction, bitwise operations, and more.
- Learn the following functions: `cv.add()`, `cv.addWeighted()`, etc.

## Image Addition
You can add two images with the OpenCV function, `cv.add()`, or simply by the numpy operation `res = img1 + img2`. Both images should be of the same depth and type, or the second image can just be a scalar value.

### Note
There is a difference between OpenCV addition and Numpy addition. OpenCV addition is a saturated operation while Numpy addition is a modulo operation.

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

\[ \text{dst} = \alpha \cdot \text{img1} + \beta \cdot \text{img2} + \gamma \]

By varying \(\alpha\) from 0 to 1, you can perform a cool transition between one image to another.

Here I took two images to blend together. The first image is given a weight of 0.7 and the second image is given 0.3. `cv.addWeighted()` applies the following equation to the image:

\[ \text{dst} = 0.7 \cdot \text{img1} + 0.3 \cdot \text{img2} \]

Here \(\gamma\) is taken as zero.

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

1. **Load two images**:
    ```python
    img1 = cv.imread('messi5.jpg')
    img2 = cv.imread('opencv-logo-white.png')
    assert img1 is not None, "file could not be read, check with os.path.exists()"
    assert img2 is not None, "file could not be read, check with os.path.exists()"
    ```
    We load two images: one is the main image (`img1`) and the other is the logo (`img2`). We ensure both images are loaded correctly.

2. **Define a region of interest (ROI)**:
    ```python
    rows, cols, channels = img2.shape
    roi = img1[0:rows, 0:cols]
    ```
    We create a region of interest in `img1` that is the same size as `img2`.

3. **Create a mask of the logo and its inverse**:
    ```python
    img2gray = cv.cvtColor(img2, cv.COLOR_BGR2GRAY)
    ret, mask = cv.threshold(img2gray, 10, 255, cv.THRESH_BINARY)
    mask_inv = cv.bitwise_not(mask)
    ```
    We convert `img2` to grayscale, then create a binary mask where the logo is white and the background is black. We also create an inverse mask.

4. **Black out the area of the logo in the ROI**:
    ```python
    img1_bg = cv.bitwise_and(roi, roi, mask=mask_inv)
    ```
    Using the inverse mask, we black out the area of the logo in the ROI.

5. **Extract the logo region from the logo image**:
    ```python
    img2_fg = cv.bitwise_and(img2, img2, mask=mask)
    ```
    Using the mask, we extract the logo region from `img2`.

6. **Combine the background and the logo**:
    ```python
    dst = cv.add(img1_bg, img2_fg)
    img1[0:rows, 0:cols] = dst
    ```
    We add the background and the logo, and then place the result back into the original image.

7. **Display the result**:
    ```python
    cv.imshow('res', img1)
    cv.waitKey(0)
    cv.destroyAllWindows()
    ```
    We display the final image.

Check the result below. The left image shows the mask we created. The right image shows the final result. For better understanding, display all the intermediate images in the above code, especially `img1_bg` and `img2_fg`.

![Mask and Result](https://docs.opencv.org/4.x/overlay.jpg)

## Additional Resources
### Exercises
- Create a slideshow of images in a folder with smooth transition between images using the `cv.addWeighted` function.

---

This completes the Markdown file with the detailed explanation of arithmetic operations on images using OpenCV.

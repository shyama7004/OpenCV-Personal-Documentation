# OpenCV: Geometric Transformations of Images

## Goals
- Learn to apply different geometric transformations to images, like translation, rotation, affine transformation, etc.
- You will see these functions: `cv.getPerspectiveTransform`

## Transformations
OpenCV provides two transformation functions, `cv.warpAffine` and `cv.warpPerspective`, with which you can perform all kinds of transformations. `cv.warpAffine` takes a 2x3 transformation matrix while `cv.warpPerspective` takes a 3x3 transformation matrix as input.

### Scaling
Scaling is just resizing of the image. OpenCV comes with a function `cv.resize()` for this purpose. The size of the image can be specified manually, or you can specify the scaling factor. Different interpolation methods are used. Preferable interpolation methods are `cv.INTER_AREA` for shrinking and `cv.INTER_CUBIC` (slow) & `cv.INTER_LINEAR` for zooming. By default, the interpolation method `cv.INTER_LINEAR` is used for all resizing purposes. You can resize an input image with either of the following methods:

```python
import numpy as np
import cv2 as cv

img = cv.imread('messi5.jpg')
assert img is not None, "file could not be read, check with os.path.exists()"

res = cv.resize(img, None, fx=2, fy=2, interpolation=cv.INTER_CUBIC)

# OR

height, width = img.shape[:2]
res = cv.resize(img, (2*width, 2*height), interpolation=cv.INTER_CUBIC)
```

### Translation
Translation is the shifting of an object's location. If you know the shift in the (x, y) direction and let it be (tx, ty), you can create the transformation matrix M as follows:

<div align="center"><img src ="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/8.png" width ="300" height="150"></div>

```python
import numpy as np
import cv2 as cv

img = cv.imread('messi5.jpg', cv.IMREAD_GRAYSCALE)
assert img is not None, "file could not be read, check with os.path.exists()"
rows, cols = img.shape

M = np.float32([[1, 0, 100], [0, 1, 50]])
dst = cv.warpAffine(img, M, (cols, rows))

cv.imshow('img', dst)
cv.waitKey(0)
cv.destroyAllWindows()
```

### Explanation of code :

```py
M = np.float32([[1, 0, 100], [0, 1, 50]])
dst = cv.warpAffine(img, M, (cols, rows))
```

1. **Creating the Transformation Matrix:**
   ```python
   M = np.float32([[1, 0, 100], [0, 1, 50]])
   ```
   This line creates a 2x3 transformation matrix `M` using `numpy`. The matrix specifies an affine transformation, where the first two elements of each row are the scale and rotation factors, and the third element is the translation factor. In this case:
   - `[1, 0, 100]` means no scaling or rotation in the x-direction and a translation of 100 pixels.
   - `[0, 1, 50]` means no scaling or rotation in the y-direction and a translation of 50 pixels.

2. **Applying the Affine Transformation:**
   ```python
   dst = cv.warpAffine(img, M, (cols, rows))
   ```
   This line applies the affine transformation to the image `img` using OpenCV's `warpAffine` function. The parameters are:
   - `img`: The input image.
   - `M`: The transformation matrix.
   - `(cols, rows)`: The size of the output image, which is typically the same as the input image.

   The result is an image `dst` where the original image `img` has been translated by 100 pixels to the right and 50 pixels down.

**Warning:** The third argument of the `cv.warpAffine()` function is the size of the output image, which should be in the form of **(width, height)**. 

`Remember` 
  - Width = number of columns, and
  - Height = number of rows.

### Rotation
Rotation of an image for an angle θ is achieved by the transformation matrix of the form:

<div align="center"><img src="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/9.png" width ="300"></div>

But OpenCV provides scaled rotation with adjustable center of rotation so that you can rotate at any location you prefer. The modified transformation matrix is given by:

<div align="center"><img src="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/10.png" width ="900" height="300"></div>

To find this transformation matrix, OpenCV provides a function, `cv.getRotationMatrix2D`. Check out the below example which rotates the image by 90 degrees with respect to the center without any scaling.

For futher `explanation`  click on : [Matrix Explained](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/2.5.md)

```python
img = cv.imread('messi5.jpg', cv.IMREAD_GRAYSCALE)
assert img is not None, "file could not be read, check with os.path.exists()"
rows, cols = img.shape

# cols-1 and rows-1 are the coordinate limits.
M = cv.getRotationMatrix2D(((cols-1)/2.0, (rows-1)/2.0), 90, 1)
dst = cv.warpAffine(img, M, (cols, rows))
```
### Explanation:

```python
M = cv.getRotationMatrix2D((((cols-1)/2.0),((rows-1)/2.0)), 90, 1)
```

Here's what each part of the function does:

1. **`cv.getRotationMatrix2D`**: This is an OpenCV function that calculates the 2D rotation matrix for an image.

2. **`((cols-1)/2.0, (rows-1)/2.0)`**: These are the coordinates of the center of the image around which the rotation will occur.
   - `cols` is the number of columns in the image (width).
   - `rows` is the number of rows in the image (height).
   - `((cols-1)/2.0, (rows-1)/2.0)` computes the center of the image by dividing the width and height by 2. Subtracting 1 and dividing by 2.0 ensures the center point is calculated correctly, accounting for zero-based indexing.

3. **`90`**: This is the angle of rotation in degrees. Here, it specifies a rotation of 90 degrees.

4. **`1`**: This is the scale factor. A scale of 1 means the image will be rotated without scaling (i.e., the image size will remain the same).

Putting it all together:
- The `cv.getRotationMatrix2D` function computes the rotation matrix \(M\) needed to rotate an image by 90 degrees around its center point, without changing the image size.

This matrix M can then be used with the `cv.warpAffine` function to apply the rotation to the image. Here's an example of how you might use it in a full context:

```python
import cv2 as cv

# Load the image
image = cv.imread('path_to_image.jpg')

# Get the dimensions of the image
(rows, cols) = image.shape[:2]

# Compute the rotation matrix
M = cv.getRotationMatrix2D(((cols-1)/2.0, (rows-1)/2.0), 90, 1)

# Apply the rotation to the image
rotated_image = cv.warpAffine(image, M, (cols, rows))

# Save or display the rotated image
cv.imwrite('rotated_image.jpg', rotated_image)
# or
cv.imshow('Rotated Image', rotated_image)
cv.waitKey(0)
cv.destroyAllWindows()
```

In this example, the image is loaded, its center is computed, the rotation matrix is calculated, and then the image is rotated and displayed or saved.

### Results:
![Image-1](https://docs.opencv.org/5.x/rotation.jpg)

### Affine Transformation
In affine transformation, all parallel lines in the original image will still be parallel in the output image. To find the transformation matrix, we need three points from the input image and their corresponding locations in the output image. Then `cv.getAffineTransform` will create a 2x3 matrix which is to be passed to `cv.warpAffine`.

Check the below example, and also look at the points I selected (which are marked in green color):

```python
img = cv.imread('drawing.png')
assert img is not None, "file could not be read, check with os.path.exists()"
rows, cols, ch = img.shape

pts1 = np.float32([[50, 50], [200, 50], [50, 200]])
pts2 = np.float32([[10, 100], [200, 50], [100, 250]])

M = cv.getAffineTransform(pts1, pts2)

dst = cv.warpAffine(img, M, (cols, rows))

plt.subplot(121), plt.imshow(img), plt.title('Input')
plt.subplot(122), plt.imshow(dst), plt.title('Output')
plt.show()
```

### Results
![Image-2](https://docs.opencv.org/5.x/affine.jpg)

### Perspective Transformation
For perspective transformation, you need a 3x3 transformation matrix. Straight lines will remain straight even after the transformation. To find this transformation matrix, you need 4 points on the input image and corresponding points on the output image. Among these 4 points, 3 of them should not be collinear. Then the transformation matrix can be found by the function `cv.getPerspectiveTransform`. Then apply `cv.warpPerspective` with this 3x3 transformation matrix.

See the code below:

```python
img = cv.imread('sudoku.png')
assert img is not None, "file could not be read, check with os.path.exists()"
rows, cols, ch = img.shape

pts1 = np.float32([[56, 65], [368, 52], [28, 387], [389, 390]])
pts2 = np.float32([[0, 0], [300, 0], [0, 300], [300, 300]])

M = cv.getPerspectiveTransform(pts1, pts2)

dst = cv.warpPerspective(img, M, (300, 300))

plt.subplot(121), plt.imshow(img), plt.title('Input')
plt.subplot(122), plt.imshow(dst), plt.title('Output')
plt.show()
```
### Results
![Image-3](https://docs.opencv.org/5.x/perspective.jpg)

## Additional Resources
1. "Computer Vision: Algorithms and Applications", Richard Szeliski

## Exercises
```



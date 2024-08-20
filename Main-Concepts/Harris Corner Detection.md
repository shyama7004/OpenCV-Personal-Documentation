# Harris Corner Detection

## Goal

In this chapter,

- We will understand the concepts behind Harris Corner Detection.
- We will see the following functions: `cv.cornerHarris()`, `cv.cornerSubPix()`

## Theory
In the last chapter, we saw that corners are regions in the image with large variation in intensity in all the directions. One early attempt to find these corners was done by Chris Harris & Mike Stephens in their paper *A Combined Corner and Edge Detector* in 1988, so now it is called the Harris Corner Detector. He took this simple idea to a mathematical form. It basically finds the difference in intensity for a displacement of (u,v) in all directions. This is expressed as below:

<div align = center><img src = https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/22.png width =600 height =100></div>

The window function \(w(x,y)\) is either a rectangular window or a Gaussian window which gives weights to pixels underneath.

If you don't remember Gaussian window looks something like below

<div align ="center"><img src = "https://i.sstatic.net/aXMJ3.png" width =600 ></div>

We have to maximize this function <em>E(u,v)</em> for corner detection. That means we have to maximize the second term. Applying Taylor Expansion to the above equation and using some mathematical steps (please refer to any standard textbooks you like for full derivation), we get the final equation as:


<div align = center><img src = https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/23.png width =600 height =200></div>

Here, I<sub>x</sub> and I<sub>y</sub> are image derivatives in x and y directions respectively. (These can be easily found using `cv.Sobel()`).

<details>
<summary>Click here to know about acv.Sobel() use case</summary>

```cpp
import numpy as np
import cv2 as cv

# Load the image
img = cv.imread("images/messi.jpg")#replace images/messi.jpg by your own image path
if img is None:
    print("The image wasn't found.")
    exit()

# Apply the Sobel operator to compute the gradient
gradient_x = cv.Sobel(img, cv.CV_64F, 1, 0, ksize=3)  # Gradient in x direction
gradient_y = cv.Sobel(img, cv.CV_64F, 0, 1, ksize=3)  # Gradient in y direction

# Combine gradients (optional)
gradient = cv.magnitude(gradient_x, gradient_y)

# Print the gradient matrix
print(gradient)

# Optionally, display the gradients
cv.imshow("Gradient X", gradient_x)
cv.imshow("Gradient Y", gradient_y)
cv.imshow("Gradient Magnitude", gradient)
cv.waitKey(0)
cv.destroyAllWindows()
```
</details>
Then comes the main part. After this, they created a score, basically an equation, which determines if a window can contain a corner or not.

<div align = center><img src = https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/24.png width =600 height =100></div>

where 
- det(M) = &lambda;<sub>1</sub>.&lambda;<sub>2</sub>
- trace(M) = &lambda;<sub>1</sub> + &lambda;<sub>2</sub>
- &lambda;<sub>1</sub> and &lambda;<sub>2</sub> are the eigenvalues of M

So the magnitudes of these eigenvalues decide whether a region is a corner, an edge, or flat.

- When |R| is small, which happens when &lambda;<sub>1</sub> and &lambda;<sub>2</sub> are small, the region is flat.
- When R < 0, which happens when &lambda;<sub>1</sub> >> &lambda;<sub>2</sub> or vice versa, the region is edge.
- When R is large, which happens when &lambda;<sub>1</sub> and &lambda;<sub>2</sub> are large and &lambda;<sub>1</sub> ~ &lambda;<sub>2</sub>, the region is a corner.

It can be represented in a nice picture as follows:

![Harris Corner Detection](https://docs.opencv.org/4.x/harris_region.jpg)

So the result of Harris Corner Detection is a grayscale image with these scores. Thresholding for a suitable score gives you the corners in the image. We will do it with a simple image.

## Harris Corner Detector in OpenCV
OpenCV has the function `cv.cornerHarris()` for this purpose. Its arguments are:

- `img` - Input image. It should be grayscale and float32 type.
- `blockSize` - It is the size of neighbourhood considered for corner detection
- `ksize` - Aperture parameter of the Sobel derivative used.
- `k` - Harris detector free parameter in the equation.

See the example below:

```python
import numpy as np
import cv2 as cv

filename = 'chessboard.png'
img = cv.imread(filename)
gray = cv.cvtColor(img, cv.COLOR_BGR2GRAY)

gray = np.float32(gray)
dst = cv.cornerHarris(gray, 2, 3, 0.04)

# result is dilated for marking the corners, not important
dst = cv.dilate(dst, None)

# Threshold for an optimal value, it may vary depending on the image.
img[dst > 0.01 * dst.max()] = [0, 0, 255]

cv.imshow('dst', img)
if cv.waitKey(0) & 0xff == 27:
    cv.destroyAllWindows()
```

For more code information : [Click here](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/11.1.md)

Below are the three results:

![Harris Corner Detector Results](https://docs.opencv.org/4.x/harris_result.jpg)

## Corner with SubPixel Accuracy
Sometimes, you may need to find the corners with maximum accuracy. OpenCV comes with a function `cv.cornerSubPix()` which further refines the corners detected with sub-pixel accuracy. Below is an example. As usual, we need to find the Harris corners first. Then we pass the centroids of these corners (There may be a bunch of pixels at a corner, we take their centroid) to refine them. Harris corners are marked in red pixels and refined corners are marked in green pixels. For this function, we have to define the criteria when to stop the iteration. We stop it after a specified number of iterations or a certain accuracy is achieved, whichever occurs first. We also need to define the size of the neighbourhood it searches for corners.

```python
import numpy as np
import cv2 as cv

filename = 'chessboard2.jpg'
img = cv.imread(filename)
gray = cv.cvtColor(img, cv.COLOR_BGR2GRAY)

# find Harris corners
gray = np.float32(gray)
dst = cv.cornerHarris(gray, 2, 3, 0.04)
dst = cv.dilate(dst, None)
ret, dst = cv.threshold(dst, 0.01 * dst.max(), 255, 0)
dst = np.uint8(dst)

# find centroids
ret, labels, stats, centroids = cv.connectedComponentsWithStats(dst)

# define the criteria to stop and refine the corners
criteria = (cv.TERM_CRITERIA_EPS + cv.TERM_CRITERIA_MAX_ITER, 100, 0.001)
corners = cv.cornerSubPix(gray, np.float32(centroids), (5, 5), (-1, -1), criteria)

# Now draw them
res = np.hstack((centroids, corners))
res = np.int0(res)
img[res[:, 1], res[:, 0]] = [0, 0, 255]
img[res[:, 3], res[:, 2]] = [0, 255, 0]

cv.imwrite('subpixel5.png', img)
```

Below is the result, where some important locations are shown in the zoomed window to visualize:

![Subpixel Accuracy](https://docs.opencv.org/4.x/subpixel3.png)

## Additional Resources
- Exercises
```

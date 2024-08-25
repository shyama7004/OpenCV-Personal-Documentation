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
<summary>Click here to know about cv.Sobel() use case</summary>

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

# Load the image
img = cv.imread("images/pentagon.png")
assert img is not None, "File wasn't found, check for os.path.exists()"

# Convert to grayscale
gray = cv.cvtColor(img, cv.COLOR_BGR2GRAY)
gray = np.float32(gray)

# Detect corners using Harris Corner Detection
corner = cv.cornerHarris(gray, 2, 3, 0.04)
corner = cv.dilate(corner, None)

# Threshold to mark the corners
ret, corner = cv.threshold(corner, 0.01 * corner.max(), 255, 0)
corner = np.uint8(corner)

# Find connected components
ret, labels, stats, centroids = cv.connectedComponentsWithStats(corner)

# Define criteria for sub-pixel accuracy
stop = (cv.TERM_CRITERIA_EPS + cv.TERM_CRITERIA_MAX_ITER, 100, 0.001)

# Refine corner locations to sub-pixel accuracy
corners = cv.cornerSubPix(gray, np.float32(centroids), (5, 5), (-1, -1), stop)

# Concatenate centroids and refined corners
result = np.hstack((centroids, corners))
result = np.int0(result)

# Draw the centroids and corners on the image
img[result[:, 1], result[:, 0]] = [0, 0, 255]  # Centroids marked in red
img[result[:, 3], result[:, 2]] = [0, 255, 0]  # Refined corners marked in green

# Display the result
cv.imshow('Corners & Centroids', img)
cv.waitKey(0)
cv.destroyAllWindows()
```
<details>
<summary>Code Explanation</summary>

## Explanation

```python
# Detect corners using Harris Corner Detection
corner = cv.cornerHarris(gray, 2, 3, 0.04)
corner = cv.dilate(corner, None)
```
- **`corner = cv.cornerHarris(gray, 2, 3, 0.04)`**: Applies the Harris Corner Detection algorithm to detect corners in the grayscale image. The parameters are:
  - `gray`: Input grayscale image.
  - `2`: Block size, which is the neighborhood size considered for corner detection.
  - `3`: Aperture parameter for the Sobel operator (used internally in corner detection).
  - `0.04`: Harris detector free parameter (typically between 0.04 to 0.06).
- **`corner = cv.dilate(corner, None)`**: Dilates the corner regions to make them more prominent in the output image. Dilation increases the thickness of the corner points detected.

```python
# Threshold to mark the corners
ret, corner = cv.threshold(corner, 0.01 * corner.max(), 255, 0)
corner = np.uint8(corner)
```
- **`ret, corner = cv.threshold(corner, 0.01 * corner.max(), 255, 0)`**: Applies a binary threshold to the corner detection result to mark the corners. The threshold value is set to 1% of the maximum corner response value. If a pixel value is greater than the threshold, it is set to 255 (white); otherwise, it is set to 0 (black).
- **`corner = np.uint8(corner)`**: Converts the result of the thresholding to an unsigned 8-bit integer type, which is the standard format for image data.

```python
# Find connected components
ret, labels, stats, centroids = cv.connectedComponentsWithStats(corner)
```
- **`ret, labels, stats, centroids = cv.connectedComponentsWithStats(corner)`**: Finds connected components in the binary image (`corner`). It returns:
  - `ret`: The number of connected components.
  - `labels`: An array where each element has a label indicating which connected component it belongs to.
  - `stats`: Statistics about each connected component (e.g., bounding box, area).
  - `centroids`: The centroids (center points) of each connected component.

```python
# Define criteria for sub-pixel accuracy
stop = (cv.TERM_CRITERIA_EPS + cv.TERM_CRITERIA_MAX_ITER, 100, 0.001)
```
- **`stop = (cv.TERM_CRITERIA_EPS + cv.TERM_CRITERIA_MAX_ITER, 100, 0.001)`**: Defines the termination criteria for the sub-pixel corner refinement. It stops the algorithm when:
  - `cv.TERM_CRITERIA_EPS + cv.TERM_CRITERIA_MAX_ITER`: Either the specified accuracy (`EPS`) or the maximum number of iterations (`MAX_ITER`) is reached.
  - `100`: The maximum number of iterations.
  - `0.001`: The desired accuracy (epsilon) for corner refinement.

```python
# Refine corner locations to sub-pixel accuracy
corners = cv.cornerSubPix(gray, np.float32(centroids), (5, 5), (-1, -1), stop)
```
- **`corners = cv.cornerSubPix(gray, np.float32(centroids), (5, 5), (-1, -1), stop)`**: Refines the corner positions to sub-pixel accuracy based on the centroids found earlier. It uses the grayscale image as input, and the centroids as initial estimates. The parameters `(5, 5)` define the half-size of the search window, and `(-1, -1)` specifies the region of interest (full image in this case).

```python
# Concatenate centroids and refined corners
result = np.hstack((centroids, corners))
result = np.int0(result)
```
- **`result = np.hstack((centroids, corners))`**: Horizontally stacks the centroids and their corresponding refined corners into a single array for easier processing.
- **`result = np.int0(result)`**: Converts the resulting array to integer format, ensuring that the coordinates are in pixel format (integer values).

```python
# Draw the centroids and corners on the image
img[result[:, 1], result[:, 0]] = [0, 0, 255]  # Centroids marked in red
img[result[:, 3], result[:, 2]] = [0, 255, 0]  # Refined corners marked in green
```
- **`img[result[:, 1], result[:, 0]] = [0, 0, 255]`**: Marks the original centroids on the image with red color (`[0, 0, 255]` in BGR format).
- **`img[result[:, 3], result[:, 2]] = [0, 255, 0]`**: Marks the refined corners on the image with green color (`[0, 255, 0]` in BGR format).

```python
# Display the result
cv.imshow('Corners & Centroids', img)
cv.waitKey(0)
cv.destroyAllWindows()
```
- **`cv.imshow('Corners & Centroids', img)`**: Displays the image with the marked centroids and corners in a window titled "Corners & Centroids".
- **`cv.waitKey(0)`**: Waits indefinitely for a key press. This is necessary to keep the image window open.
- **`cv.destroyAllWindows()`**: Closes all OpenCV windows that were opened during the program.

</details>
Below is the result, where some important locations are shown in the zoomed window to visualize:

![Subpixel Accuracy](https://docs.opencv.org/4.x/subpixel3.png)

## Additional Resources
- Exercises
```
Crafted by shyama7004 with help of OpenCV Docs:)

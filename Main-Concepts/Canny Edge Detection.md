# Canny Edge Detection

## Goal

In this chapter, we will learn about:

- The concept of Canny edge detection
- OpenCV functions for that: `cv.Canny()`

## Theory

Canny Edge Detection is a popular edge detection algorithm. It was developed by John F. Canny in [year].

It is a multi-stage algorithm and we will go through each stage.

### Noise Reduction

Since edge detection is susceptible to noise in the image, the first step is to remove the noise in the image with a 5x5 Gaussian filter. We have already seen this in previous chapters.

### Finding Intensity Gradient of the Image

The smoothened image is then filtered with a Sobel kernel in both horizontal and vertical directions to get the first derivative in the horizontal direction (G<sub>x</sub>) and vertical direction (G<sub>y</sub>). From these two images, we can find the edge gradient and direction for each pixel as follows:

The `Sobel` Operator is a discrete differentiation operator.(Already raed before)

<div align="center"><img src="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/5.png"></div>

Gradient direction is always perpendicular to edges. It is rounded to one of four angles representing vertical, horizontal, and two diagonal directions.

### Non-maximum Suppression

After getting gradient magnitude and direction, a full scan of the image is done to remove any unwanted pixels which may not constitute the edge. For this, at every pixel, the pixel is checked if it is a local maximum in its neighborhood in the direction of the gradient. Check the image below:

![image](https://docs.opencv.org/5.x/nms.jpg)

Point A is on the edge (in the vertical direction). The gradient direction is normal to the edge. Points B and C are in gradient directions. So point A is checked with points B and C to see if it forms a local maximum. If so, it is considered for the next stage; otherwise, it is suppressed (put to zero).

In short, the result you get is a binary image with "thin edges".

### Hysteresis Thresholding

This stage decides which edges are real edges and which are not. For this, we need two threshold values, `minVal` and `maxVal`. Any edges with intensity gradient more than `maxVal` are sure to be edges, and those below `minVal` are sure to be non-edges, so discarded. Those who lie between these two thresholds are classified as edges or non-edges based on their connectivity. If they are connected to "sure-edge" pixels, they are considered to be part of edges. Otherwise, they are also discarded. See the image below:

![image](https://docs.opencv.org/5.x/hysteresis.jpg)

The edge A is above the `maxVal`, so considered as a "sure-edge". Although edge C is below `maxVal`, it is connected to edge A, so that is also considered a valid edge and we get that full curve. But edge B, although it is above `minVal` and is in the same region as that of edge C, it is not connected to any "sure-edge", so that is discarded. So it is very important that we have to select `minVal` and `maxVal` accordingly to get the correct result.

- `sure-edge` pixels refer to edges that have an intensity gradient greater than maxVal. These edges are considered definitive and are not discarded, unlike edges with intensity gradients between minVal and maxVal, which are classified based on their connectivity to “sure-edge” pixels.

This stage also removes small pixel noises on the assumption that edges are long lines.

So what we finally get are strong edges in the image.

## Canny Edge Detection in OpenCV

OpenCV puts all the above in a single function, `cv.Canny()`. We will see how to use it. The first argument is our input image. The second and third arguments are our `minVal` and `maxVal` respectively. The fourth argument is `aperture_size`. It is the size of the Sobel kernel used to find image gradients. By default, it is 3. The last argument is `L2gradient` which specifies the equation for finding gradient magnitude. If it is True, it uses the equation mentioned above which is more accurate, otherwise it uses this function: <br> Edge_Gradient(G) = |G<sub>x</sub>| + |G<sub>y</sub>| . By default, it is False.

### How to find minVal and maxVal?

Click here to find the `answer`: [minVal/maxVal](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/3.4.md)

```python
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt

img = cv.imread('messi5.jpg', cv.IMREAD_GRAYSCALE)
assert img is not None, "file could not be read, check with os.path.exists()"
edges = cv.Canny(img,100,200)

plt.subplot(121),plt.imshow(img,cmap = 'gray')
plt.title('Original Image'), plt.xticks([]), plt.yticks([])
plt.subplot(122),plt.imshow(edges,cmap = 'gray')
plt.title('Edge Image'), plt.xticks([]), plt.yticks([])

plt.show()
```

See the result below:

![image](https://docs.opencv.org/5.x/canny1.jpg)

## Additional Resources

- [Canny edge detector at Wikipedia](https://en.wikipedia.org/wiki/Canny_edge_detector)
- [Canny Edge Detection Tutorial by Bill Green, 2002](https://web.archive.org/web/20080521135521/http://www.pages.drexel.edu/~weg22/can_tut.html)

## Exercises

Write a small application to find the Canny edge detection whose threshold values can be varied using two trackbars. This way, you can understand the effect of threshold values.

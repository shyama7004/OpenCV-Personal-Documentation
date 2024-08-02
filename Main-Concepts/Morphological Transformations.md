# Morphological Transformations

## Goal
In this chapter,

- We will learn different morphological operations like Erosion, Dilation, Opening, Closing, etc.
- We will see different functions like: `cv.erode()`, `cv.dilate()`, `cv.morphologyEx()`, etc.

## Theory
Morphological transformations are some simple operations based on the image shape. It is normally performed on binary images. It needs two inputs, one is our original image, the second one is called structuring element or kernel which decides the nature of the operation. Two basic morphological operators are Erosion and Dilation. Then its variant forms like Opening, Closing, Gradient, etc. also come into play. We will see them one-by-one with help of the following image:

<div align="center"><img src="https://docs.opencv.org/5.x/j.png"></div>

### 1. Erosion
The basic idea of erosion is just like soil erosion only, it erodes away the boundaries of the foreground object (Always try to keep foreground in white). So what does it do? The kernel slides through the image (as in 2D convolution). A pixel in the original image (either 1 or 0) will be considered 1 only if all the pixels under the kernel are 1, otherwise, it is eroded (made to zero).

So what happens is that all the pixels near the boundary will be discarded depending upon the size of the kernel. So the thickness or size of the foreground object decreases or simply the white region decreases in the image. It is useful for removing small white noises (as we have seen in the colorspace chapter), detaching two connected objects, etc.

Here, as an example, I would use a 5x5 kernel with full of ones. Let's see how it works:

```python
import cv2 as cv
import numpy as np
 
img = cv.imread('j.png', cv.IMREAD_GRAYSCALE)
assert img is not None, "file could not be read, check with os.path.exists()"
kernel = np.ones((5,5),np.uint8)
erosion = cv.erode(img,kernel,iterations = 1)
```

**Result:**

<div align="center"><img src="https://docs.opencv.org/5.x/erosion.png"></div>

### 2. Dilation
It is just the opposite of erosion. Here, a pixel element is '1' if at least one pixel under the kernel is '1'. So it increases the white region in the image or the size of the foreground object increases. Normally, in cases like noise removal, erosion is followed by dilation. Because erosion removes white noises, but it also shrinks our object. So we dilate it. Since noise is gone, they won't come back, but our object area increases. It is also useful in joining broken parts of an object.

```python
dilation = cv.dilate(img,kernel,iterations = 1)
```

**Result:**

<div align="center"><img src="https://docs.opencv.org/5.x/dilation.png"></div>

### 3. Opening
Opening is just another name for erosion followed by dilation. It is useful in removing noise, as we explained above. Here we use the function, `cv.morphologyEx()`

```python
opening = cv.morphologyEx(img, cv.MORPH_OPEN, kernel)
```

**Result:**

<div align="center"><img src="https://docs.opencv.org/5.x/opening.png"></div>

### 4. Closing
Closing is the reverse of Opening, Dilation followed by Erosion. It is useful in closing small holes inside the foreground objects, or small black points on the object.

```python
closing = cv.morphologyEx(img, cv.MORPH_CLOSE, kernel)
```

**Result:**

<div align="center"><img src ="https://docs.opencv.org/5.x/closing.png"><></div>

### 5. Morphological Gradient
It is the difference between dilation and erosion of an image.

The result will look like the outline of the object.

```python
gradient = cv.morphologyEx(img, cv.MORPH_GRADIENT, kernel)
```

**Result:**

<div align="center"><img src="https://docs.opencv.org/5.x/gradient.png"></div>

### 6. Top Hat
It is the difference between the input image and the Opening of the image. Below example is done for a 9x9 kernel.

```python
tophat = cv.morphologyEx(img, cv.MORPH_TOPHAT, kernel)
```

**Result:**

<div align="center"><img src="https://docs.opencv.org/5.x/tophat.png"></div>

### 7. Black Hat
It is the difference between the closing of the input image and the input image.

```python
blackhat = cv.morphologyEx(img, cv.MORPH_BLACKHAT, kernel)
```

**Result:**

<div align="center"><img src="https://docs.opencv.org/5.x/blackhat.png"></div>

## Structuring Element
We manually created structuring elements in the previous examples with the help of Numpy. It is a rectangular shape. But in some cases, you may need elliptical/circular shaped kernels. So for this purpose, OpenCV has a function, `cv.getStructuringElement()`. You just pass the shape and size of the kernel, you get the desired kernel.

### Rectangular Kernel
```python
cv.getStructuringElement(cv.MORPH_RECT,(5,5))
```
Output:
```
array([[1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1]], dtype=uint8)
```
### Elliptical Kernel
```python
cv.getStructuringElement(cv.MORPH_ELLIPSE,(5,5))
```
Output:
```
array([[0, 0, 1, 0, 0],
       [1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1],
       [0, 0, 1, 0, 0]], dtype=uint8)
```
### Cross-shaped Kernel
```python
cv.getStructuringElement(cv.MORPH_CROSS,(5,5))
```
Output:
```
array([[0, 0, 1, 0, 0],
       [0, 0, 1, 0, 0],
       [1, 1, 1, 1, 1],
       [0, 0, 1, 0, 0],
       [0, 0, 1, 0, 0]], dtype=uint8)
```

## Additional Resources
- Morphological Operations at HIPR2
- Exercises

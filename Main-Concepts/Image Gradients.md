# OpenCV: Image Gradients

## Goal

In this chapter, we will learn to:

- Find Image gradients, edges etc.
- We will see following functions: `cv.Sobel()`, `cv.Scharr()`, `cv.Laplacian()` etc.

## Theory

OpenCV provides three types of gradient filters or High-pass filters: Sobel, Scharr, and Laplacian. We will see each one of them.

### 1. Sobel and Scharr Derivatives

Sobel operators perform a joint Gaussian smoothing plus differentiation operation, making it more resistant to noise. You can specify the direction of derivatives to be taken, vertical or horizontal (by the arguments `yorder` and `xorder` respectively). You can also specify the size of the kernel by the argument `ksize`. If `ksize = -1`, a 3x3 Scharr filter is used which gives better results than a 3x3 Sobel filter. Please see the docs for kernels used.

### 2. Laplacian Derivatives

It calculates the Laplacian of the image given by the relation:

![Image-1](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/11.png)

where each derivative is found using Sobel derivatives. If `ksize = 1`, then the following kernel is used for filtering:

![Image-2](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/12.png)

### Code

Below code shows all operators in a single diagram. All kernels are of 5x5 size. The depth of the output image is passed as -1 to get the result in `np.uint8` type.

```python
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt

img = cv.imread('dave.jpg', cv.IMREAD_GRAYSCALE)
assert img is not None, "file could not be read, check with os.path.exists()"

laplacian = cv.Laplacian(img, cv.CV_64F)
sobelx = cv.Sobel(img, cv.CV_64F, 1, 0, ksize=5)
sobely = cv.Sobel(img, cv.CV_64F, 0, 1, ksize=5)

plt.subplot(2, 2, 1), plt.imshow(img, cmap='gray')
plt.title('Original'), plt.xticks([]), plt.yticks([])
plt.subplot(2, 2, 2), plt.imshow(laplacian, cmap='gray')
plt.title('Laplacian'), plt.xticks([]), plt.yticks([])
plt.subplot(2, 2, 3), plt.imshow(sobelx, cmap='gray')
plt.title('Sobel X'), plt.xticks([]), plt.yticks([])
plt.subplot(2, 2, 4), plt.imshow(sobely, cmap='gray')
plt.title('Sobel Y'), plt.xticks([]), plt.yticks([])

plt.show()
```

`Colormaps` or `Cmap` in python colormaps is a very useful tool for data visualization. Matlibpro comes with a number of built-in colormaps, such as sequential, diverging, cyclic, qualitative, miscellaneous, etc. Y

Image:

![Image-3](https://docs.opencv.org/4.x/gradients.jpg)

### Result

One Important Matter!

In our last example, the output datatype is `cv.CV_8U` or `np.uint8`. But there is a slight problem with that. Black-to-White transition is taken as a positive slope (it has a positive value) while White-to-Black transition is taken as a negative slope (It has a negative value). So when you convert data to `np.uint8`, all negative slopes are made zero. In simple words, you miss that edge.

If you want to detect both edges, a better option is to keep the output datatype to some higher forms, like `cv.CV_16S`, `cv.CV_64F` etc., take its absolute value, and then convert back to `cv.CV_8U`.

Below code demonstrates this procedure for a horizontal Sobel filter and the difference in results.

```python
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt

img = cv.imread('box.png', cv.IMREAD_GRAYSCALE)
assert img is not None, "file could not be read, check with os.path.exists()"

# Output dtype = cv.CV_8U
sobelx8u = cv.Sobel(img, cv.CV_8U, 1, 0, ksize=5)

# Output dtype = cv.CV_64F. Then take its absolute and convert to cv.CV_8U
sobelx64f = cv.Sobel(img, cv.CV_64F, 1, 0, ksize=5)
abs_sobel64f = np.absolute(sobelx64f)
sobel_8u = np.uint8(abs_sobel64f)

plt.subplot(1, 3, 1), plt.imshow(img, cmap='gray')
plt.title('Original'), plt.xticks([]), plt.yticks([])
plt.subplot(1, 3, 2), plt.imshow(sobelx8u, cmap='gray')
plt.title('Sobel CV_8U'), plt.xticks([]), plt.yticks([])
plt.subplot(1, 3, 3), plt.imshow(sobel_8u, cmap='gray')
plt.title('Sobel abs(CV_64F)'), plt.xticks([]), plt.yticks([])

plt.show()
```
Check the result below:

![Image-4](https://docs.opencv.org/4.x/double_edge.jpg)


### Additional Resources

Exercises

Generated on Sat Aug 3, 2024 23:25:58 for OpenCV by doxygen 1.9.8

[OpenCV Documentation](https://docs.opencv.org/5.x/d5/d0f/tutorial_py_gradients.html)

[Image Processing in OpenCV](https://docs.opencv.org/5.x/d4/d86/group__imgproc__filter.html#gacea54f142e81b6758cb6f375ce782c8d)


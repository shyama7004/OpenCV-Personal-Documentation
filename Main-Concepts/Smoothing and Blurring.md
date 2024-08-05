# Smoothing Images

## Goals
Learn to:
- Blur images with various low pass filters
- Apply custom-made filters to images (2D convolution)

## 2D Convolution (Image Filtering)
As in one-dimensional signals, images also can be filtered with various low-pass filters (LPF), high-pass filters (HPF), etc. LPF helps in removing noise, blurring images, etc. HPF filters help in finding edges in images.

🔬 `A low-pass filter` is a type of electrical circuit that allows low-frequency signals to pass through while blocking or reducing high-frequency signals

OpenCV provides a function `cv.filter2D()` to convolve a kernel with an image. As an example, we will try an averaging filter on an image. A 5x5 averaging filter kernel will look like the below:

🌐 In OpenCV, a kernel (or convolution matrix) is a small matrix used for image processing operations, such as filtering or edge detection. It's applied to the image through a process called convolution, which involves element-wise multiplication and summation, to achieve a specific effect. For example, a Gaussian kernel can be used for blurring an image.

![Formula1](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/1.png)

The operation works like this: keep this kernel above a pixel, add all the 25 pixels below this kernel, take the average, and replace the central pixel with the new average value. This operation is continued for all the pixels in the image. Try this code and check the result:

```python
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt

img = cv.imread('opencv_logo.png')
assert img is not None, "file could not be read, check with os.path.exists()"

kernel = np.ones((5,5),np.float32)/25
dst = cv.filter2D(img,-1,kernel)

plt.subplot(121),plt.imshow(img),plt.title('Original')
plt.xticks([]), plt.yticks([])
plt.subplot(122),plt.imshow(dst),plt.title('Averaging')
plt.xticks([]), plt.yticks([])
plt.show()
```
`Result :`

![Image](https://docs.opencv.org/5.x/filter.jpg)

## Image Blurring (Image Smoothing)
Image blurring is achieved by convolving the image with a low-pass filter kernel. It is useful for removing noise. It actually removes high frequency content (e.g., noise, edges) from the image. So edges are blurred a little bit in this operation (there are also blurring techniques which don't blur the edges). OpenCV provides four main types of blurring techniques.

### 1. Averaging
This is done by convolving an image with a normalized box filter. It simply takes the average of all the pixels under the kernel area and replaces the central element. This is done by the function `cv.blur()` or `cv.boxFilter()`. Check the docs for more details about the kernel. We should specify the width and height of the kernel. A 3x3 normalized box filter would look like the below:

![Formula-2](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/2.png)

> Note: If you don't want to use a normalized box filter, use `cv.boxFilter()`. Pass an argument `normalize=False` to the function. Check a sample demo below with a kernel of 5x5 size:

```python
import cv2 as cv
import numpy as np
from matplotlib import pyplot as plt

img = cv.imread('opencv-logo-white.png')
assert img is not None, "file could not be read, check with os.path.exists()"

blur = cv.blur(img,(5,5))

plt.subplot(121),plt.imshow(img),plt.title('Original')
plt.xticks([]), plt.yticks([])
plt.subplot(122),plt.imshow(blur),plt.title('Blurred')
plt.xticks([]), plt.yticks([])
plt.show()
```
`Explanation` of xticks and yticks: [Click here](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/3.1.md)

![Image-2](https://docs.opencv.org/5.x/blur.jpg)

### 2. Gaussian Blurring
In this method, instead of a box filter, a Gaussian kernel is used. It is done with the function, `cv.GaussianBlur()`. We should specify the width and height of the kernel which should be positive and odd. We also should specify the standard deviation in the X and Y directions, `sigmaX` and `sigmaY` respectively. If only `sigmaX` is specified, `sigmaY` is taken as the same as `sigmaX`. If both are given as zeros, they are calculated from the kernel size. Gaussian blurring is highly effective in removing Gaussian noise from an image.

If you want, you can create a Gaussian kernel with the function, `cv.getGaussianKernel()`.

The above code can be modified for Gaussian blurring:

```python
blur = cv.GaussianBlur(img,(5,5),0)
```

### 3. Median Blurring
Here, the function `cv.medianBlur()` takes the median of all the pixels under the kernel area and the central element is replaced with this median value. This is highly effective against salt-and-pepper noise in an image. Interestingly, in the above filters, the central element is a newly calculated value which may be a pixel value in the image or a new value. But in median blurring, the central element is always replaced by some pixel value in the image. It reduces the noise effectively. Its kernel size should be a positive odd integer.

In this demo, I added a 50% noise to our original image and applied median blurring. Check the result:

```python
median = cv.medianBlur(img,5)
```

### 4. Bilateral Filtering
`cv.bilateralFilter()` is highly effective in noise removal while keeping edges sharp. But the operation is slower compared to other filters. We already saw that a Gaussian filter takes the neighborhood around the pixel and finds its Gaussian weighted average. This Gaussian filter is a function of space alone, that is, nearby pixels are considered while filtering. It doesn't consider whether pixels have almost the same intensity. It doesn't consider whether a pixel is an edge pixel or not. So it blurs the edges also, which we don't want to do.

Bilateral filtering also takes a Gaussian filter in space, but one more Gaussian filter which is a function of pixel difference. The Gaussian function of space makes sure that only nearby pixels are considered for blurring, while the Gaussian function of intensity difference makes sure that only those pixels with similar intensities to the central pixel are considered for blurring. So it preserves the edges since pixels at edges will have large intensity variation.

The below sample shows the use of a bilateral filter (For details on arguments, visit docs).

```python
blur = cv.bilateralFilter(img,9,75,75)
```

See, the texture on the surface is gone, but the edges are still preserved.

`Result:`

![img](https://docs.opencv.org/5.x/bilateral.jpg)

## Additional Resources
- Details about the [bilateral filtering](https://people.csail.mit.edu/sparis/bf_course/)
- Exercises

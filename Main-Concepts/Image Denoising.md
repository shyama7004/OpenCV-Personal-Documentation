# Image Denoising

## Goal
In this chapter:

- You will learn about the Non-local Means Denoising algorithm to remove noise in the image.
- You will see different functions like `cv.fastNlMeansDenoising()`, `cv.fastNlMeansDenoisingColored()`, etc.

## Theory
In earlier chapters, we have seen many image smoothing techniques like Gaussian Blurring, Median Blurring, etc., and they were good to some extent in removing small quantities of noise. In those techniques, we took a small neighborhood around a pixel and did some operations like Gaussian weighted average, median of the values, etc., to replace the central element. In short, noise removal at a pixel was local to its neighborhood.

There is a property of noise. Noise is generally considered to be a random variable with zero mean. Consider a noisy pixel,<em> p = p<sub>0</sub> + n </em> where p<sub>0</sub> is the true value of the pixel and the noise in that pixel is <strong>n</strong>. You can take a large number of the same pixels (say <strong>N</strong>) from different images and compute their average. Ideally, you should get p = p<sub>0</sub> since the mean of the noise is zero.

You can verify it yourself by a simple setup. Hold a static camera at a certain location for a couple of seconds. This will give you plenty of frames, or a lot of images of the same scene. Then write a piece of code to find the average of all the frames in the video (This should be too simple for you now). Compare the final result and the first frame. You can see a reduction in noise. Unfortunately, this simple method is not robust to camera and scene motions. Also, often there is only one noisy image available.

So the idea is simple: we need a set of similar images to average out the noise. Consider a small window (say a 5x5 window) in the image. There is a large chance that the same patch may be somewhere else in the image, sometimes in a small neighborhood around it. What about using these similar patches together and finding their average? For that particular window, that is fine. See an example image below:

![image](https://docs.opencv.org/5.x/nlm_patch.jpg)

The blue patches in the image look similar. The green patches look similar. So we take a pixel, take a small window around it, search for similar windows in the image, average all the windows, and replace the pixel with the result we got. This method is Non-Local Means Denoising. It takes more time compared to the blurring techniques we saw earlier, but its result is very good. More details and an online demo can be found at the first link in additional resources.

For color images, the image is converted to the `CIELAB` colorspace, and then it separately denoises the L and AB components.

`CIELAB or `CIE` `L*a*b*` is a device-independent, 3D color space that enables accurate measurement and comparison of all perceivable colors using three color values.

## Image Denoising in OpenCV
OpenCV provides four variations of this technique:

- `cv.fastNlMeansDenoising()` - works with single grayscale images.
- `cv.fastNlMeansDenoisingColored()` - works with a color image.
- `cv.fastNlMeansDenoisingMulti()` - works with an image sequence captured in a short period of time (grayscale images).
- `cv.fastNlMeansDenoisingColoredMulti()` - same as above, but for color images.

Common arguments are:

- `h`: parameter deciding filter strength. A higher `h` value removes noise better but also removes details of the image. (10 is ok)
- `hForColorComponents`: same as `h`, but for color images only. (normally same as `h`)
- `templateWindowSize`: should be odd. (recommended 7)
- `searchWindowSize`: should be odd. (recommended 21)

Please visit the first link in additional resources for more details on these parameters.

We will demonstrate 2 and 3 here. The rest is left for you.

### 1. `cv.fastNlMeansDenoisingColored()`
As mentioned above, it is used to remove noise from color images. (Noise is expected to be Gaussian). See the example below:

```python
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt
 
img = cv.imread('die.png')
 
dst = cv.fastNlMeansDenoisingColored(img, None, 10, 10, 7, 21)
 
plt.subplot(121), plt.imshow(img)
plt.subplot(122), plt.imshow(dst)
plt.show()
```
### Explanation

```python
dst = cv.fastNlMeansDenoisingColored(img, None, 10, 10, 7, 21)
```
  - Parameters:
    - `img`: The input image that you want to denoise.
    - `None`: The output image placeholder (None implies that a new image will be created).
    - `10`: The strength of the luminance (color) noise filtering. Higher values remove more noise but can blur details.
    - `10`: The strength of the color noise filtering.
    - `7`: The size of the window used to compute the weighted average for a given pixel.
    - `21`: The size of the neighborhood to search for similar patches. A larger search area may result in better noise reduction but will take longer to compute.


Below is a zoomed version of the result. My input image has a Gaussian noise of &sigma;. See the result:

![image](https://docs.opencv.org/5.x/nlm_result1.jpg)

### 2. `cv.fastNlMeansDenoisingMulti()`
Now we will apply the same method to a video. The first argument is the list of noisy frames. The second argument `imgToDenoiseIndex` specifies which frame we need to denoise; for that, we pass the index of the frame in our input list. The third is the `temporalWindowSize`, which specifies the number of nearby frames to be used for denoising. It should be odd. In that case, a total of `temporalWindowSize` frames are used where the central frame is the frame to be denoised. For example, you passed a list of 5 frames as input. Let `imgToDenoiseIndex = 2` and `temporalWindowSize = 3`. Then frame-1, frame-2, and frame-3 are used to denoise frame-2. Let's see an example:

```python
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt
 
cap = cv.VideoCapture('vtest.avi')
 
# create a list of the first 5 frames
img = [cap.read()[1] for i in range(5)]
 
# convert all to grayscale
gray = [cv.cvtColor(i, cv.COLOR_BGR2GRAY) for i in img]
 
# convert all to float64
gray = [np.float64(i) for i in gray]
 
# create noise of variance 25
noise = np.random.randn(*gray[1].shape) * 10
 
# Add this noise to images
noisy = [i + noise for i in gray]
 
# Convert back to uint8
noisy = [np.uint8(np.clip(i, 0, 255)) for i in noisy]
 
# Denoise 3rd frame considering all the 5 frames
dst = cv.fastNlMeansDenoisingMulti(noisy, 2, 5, None, 4, 7, 35)
 
plt.subplot(131), plt.imshow(gray[2], 'gray')
plt.subplot(132), plt.imshow(noisy[2], 'gray')
plt.subplot(133), plt.imshow(dst, 'gray')
plt.show()
```



Below image shows a zoomed version of the result we got:

![image](https://docs.opencv.org/5.x/nlm_multi.jpg)

For more code expalanation : [click here](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/3.11.md)

It takes a considerable amount of time for computation. In the result, the first image is the original frame, the second is the noisy one, and the third is the denoised image.
```

Created by shyama7004 with help of Opencv Docs :)

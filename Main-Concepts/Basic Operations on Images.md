# OpenCV Pixel Manipulation and Image Processing

## Goal
Learn to:
- Access pixel values and modify them
- Access image properties
- Set a Region of Interest (ROI)
- Split and merge images

Almost all the operations in this section are mainly related to Numpy rather than OpenCV. A good knowledge of Numpy is required to write better-optimized code with OpenCV.

*(Examples will be shown in a Python terminal, since most of them are just single lines of code)*

## Accessing and Modifying Pixel Values
Let's load a color image first:

```python
>>> import numpy as np
>>> import cv2 as cv

>>> img = cv.imread('messi5.jpg')
>>> assert img is not None, "file could not be read, check with os.path.exists()"
```

This code reads the assigned image.

You can access a pixel value by its row and column coordinates. For a BGR image, it returns an array of Blue, Green, Red values. For a grayscale image, just the corresponding intensity is returned.

```python
>>> px = img[100,100]
>>> print(px)
[157 166 200]

# Accessing only blue pixel
>>> blue = img[100,100,0]
>>> print(blue)
157
```

You can modify the pixel values the same way.

```python
>>> img[100,100] = [255,255,255]
>>> print(img[100,100])
[255 255 255]
```

### Warning
Numpy is an optimized library for fast array calculations. Simply accessing each and every pixel value and modifying it will be very slow and it is discouraged.

### Note
The above method is normally used for selecting a region of an array, say the first 5 rows and last 3 columns. For individual pixel access, the Numpy array methods, `array.item()` and `array.itemset()` are considered better. They always return a scalar, however, so if you want to access all the B, G, R values, you will need to call `array.item()` separately for each value.

Better pixel accessing and editing method:
```py
import cv2 as cv

# Load the image
img = cv.imread("/Users/sankarsanbisoyi/Desktop/Python/Numpy Practice/download.jpeg")

# Ensure the image is loaded properly
if img is None:
    print("Error: Image not loaded.")
else:
    # Set the pixel value at (10, 10) in the third channel (blue, green, red -> 0, 1, 2)
    img[10, 10, 2] = 100

    # Display the modified image to verify the change
    cv.imshow("Modified Image", img)
    cv.waitKey(0)
    cv.destroyAllWindows()
```
`Note`:Maintain all the whitespaces.

## Accessing Image Properties
Image properties include the number of rows, columns, and channels; type of image data; number of pixels; etc.

The shape of an image is accessed by `img.shape`. It returns a tuple of the number of rows, columns, and channels (if the image is color):

```python
>>> print(img.shape)
(342, 548, 3)
```

### Note
If an image is grayscale, the tuple returned contains only the number of rows and columns, so it is a good method to check whether the loaded image is grayscale or color.

Total number of pixels is accessed by `img.size`:

```python
>>> print(img.size)
562248
```

Image datatype is obtained by `img.dtype`:

```python
>>> print(img.dtype)
uint8
```

### Note
`img.dtype` is very important while debugging because a large number of errors in OpenCV-Python code are caused by invalid datatype.

## Image ROI
Sometimes, you will have to play with certain regions of images. For eye detection in images, first face detection is done over the entire image. When a face is obtained, we select the face region alone and search for eyes inside it instead of searching the whole image. It improves accuracy (because eyes are always on faces :D) and performance (because we search in a small area).

ROI is again obtained using Numpy indexing. Here I am selecting the ball and copying it to another region in the image:

```python
>>> ball = img[280:340, 330:390]
>>> img[273:333, 100:160] = ball
```

Check the results below:

![Image with ROI](https://docs.opencv.org/4.x/roi.jpg)

## Splitting and Merging Image Channels
Sometimes you will need to work separately on the B, G, R channels of an image. In this case, you need to split the BGR image into single channels. In other cases, you may need to join these individual channels to create a BGR image. You can do this simply by:

```python
>>> b, g, r = cv.split(img)
>>> img = cv.merge((b, g, r))
```

Or:

```python
>>> b = img[:,:,0]
```

Suppose you want to set all the red pixels to zero - you do not need to split the channels first. Numpy indexing is faster:

```python
>>> img[:,:,2] = 0
```
`Note`:You can also try `[:,:,0]` & `[:,:,1]`

### Warning
`cv.split()` is a costly operation (in terms of time). So use it only if necessary. Otherwise, go for Numpy indexing.

## Making Borders for Images (Padding)
If you want to create a border around an image, something like a photo frame, you can use `cv.copyMakeBorder()`. But it has more applications for convolution operation, zero padding, etc. 

`The convolution operation` is a mathematical tool used to combine two signals or functions to produce a third signal. In the context of signals and systems, convolution relates the input signal and the impulse response of a system to produce the output signal.

This function takes the following arguments:
- `src` - input image
- `top, bottom, left, right` - border width in the number of pixels in corresponding directions
- `borderType` - Flag defining what kind of border to be added. It can be the following types:
  - `cv.BORDER_CONSTANT` - Adds a constant colored border. The value should be given as the next argument.
  - `cv.BORDER_REFLECT` - Border will be a mirror reflection of the border elements, like this: `fedcba|abcdefgh|hgfedcb`
  - `cv.BORDER_REFLECT_101` or `cv.BORDER_DEFAULT` - Same as above, but with a slight change, like this: `gfedcb|abcdefgh|gfedcba`
  - `cv.BORDER_REPLICATE` - Last element is replicated throughout, like this: `aaaaaa|abcdefgh|hhhhhhh`
  - `cv.BORDER_WRAP` - Can't explain, it will look like this: `cdefgh|abcdefgh|abcdefg`
- `value` - Color of the border if border type is `cv.BORDER_CONSTANT`

Below is a sample code demonstrating all these border types for better understanding:

```python
import cv2 as cv
import numpy as np
from matplotlib import pyplot as plt

BLUE = [255, 0, 0]

img1 = cv.imread('opencv-logo.png')
assert img1 is not None, "file could not be read, check with os.path.exists()"

replicate = cv.copyMakeBorder(img1, 10, 10, 10, 10, cv.BORDER_REPLICATE)
reflect = cv.copyMakeBorder(img1, 10, 10, 10, 10, cv.BORDER_REFLECT)
reflect101 = cv.copyMakeBorder(img1, 10, 10, 10, 10, cv.BORDER_REFLECT_101)
wrap = cv.copyMakeBorder(img1, 10, 10, 10, 10, cv.BORDER_WRAP)
constant = cv.copyMakeBorder(img1, 10, 10, 10, 10, cv.BORDER_CONSTANT, value=BLUE)

plt.subplot(231), plt.imshow(img1, 'gray'), plt.title('ORIGINAL')
plt.subplot(232), plt.imshow(replicate, 'gray'), plt.title('REPLICATE')
plt.subplot(233), plt.imshow(reflect, 'gray'), plt.title('REFLECT')
plt.subplot(234), plt.imshow(reflect101, 'gray'), plt.title('REFLECT_101')
plt.subplot(235), plt.imshow(wrap, 'gray'), plt.title('WRAP')
plt.subplot(236), plt.imshow(constant, 'gray'), plt.title('CONSTANT')

plt.show()
```
For more details click on this Link : [Code explanation](https://github.com/shyama7004/OpenCV-Personal-Documentation/tree/main/More%20Explanation)

See the result below. (Image is displayed with matplotlib. So RED and BLUE channels will be interchanged):

![Image with Borders](https://docs.opencv.org/4.x/border.jpg)



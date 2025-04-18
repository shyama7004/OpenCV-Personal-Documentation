# Image Segmentation with Watershed Algorithm

## Goal

In this chapter,

- We will learn to use marker-based image segmentation using watershed algorithm.
- We will see: `cv.watershed()`

## Theory

- Any grayscale image can be viewed as a topographic surface where:

  - High intensity denotes peaks and hills.
  - Low intensity denotes valleys.

- Start by filling every isolated valley (local minima) with different colored water (labels).

- As the water rises, water from different valleys (with different colors) will start to merge based on nearby peaks (gradients).

- To prevent merging, barriers are built in locations where water merges.

- Continue filling water and building barriers until all peaks are underwater.

- The barriers created give you the segmentation result, which is the basic concept behind the watershed algorithm.

- OpenCV's marker-based watershed algorithm addresses over-segmentation caused by noise or irregularities by allowing user interaction.

- Users specify which valley points are to be merged and which are not, making it an interactive image segmentation method.

- The process involves:

  - Labeling the region known to be the foreground or object with one color/intensity.
  - Labeling the region known to be the background or non-object with another color.
  - Labeling uncertain regions with 0, which serves as the marker.

- Apply the watershed algorithm, and the marker will be updated with the given labels, with the boundaries of objects assigned a value of -1.

## Code

Below we will see an example of how to use the Distance Transform along with watershed to segment mutually touching objects.

Consider the coins image below, the coins are touching each other. Even if you threshold it, it will be touching each other.

![image](https://docs.opencv.org/5.x/water_coins.jpg)

We start with finding an approximate estimate of the coins. For that, we can use Otsu's binarization.

```python
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt

img = cv.imread('coins.png')
assert img is not None, "file could not be read, check with os.path.exists()"
gray = cv.cvtColor(img, cv.COLOR_BGR2GRAY)
ret, thresh = cv.threshold(gray, 0, 255, cv.THRESH_BINARY_INV + cv.THRESH_OTSU)
```

**Result:**

![image](https://docs.opencv.org/5.x/water_thresh.jpg)

Now we need to remove any small white noises in the image. For that we can use morphological opening. To remove any small holes in the object, we can use morphological closing. So, now we know for sure that the region near to the center of objects is foreground and the region much away from the object is background. The only region we are not sure of is the boundary region of coins.

So we need to extract the area which we are sure they are coins. Erosion removes the boundary pixels. So whatever remains, we can be sure it is a coin. That would work if objects were not touching each other. But since they are touching each other, another good option would be to find the distance transform and apply a proper threshold. Next, we need to find the area which we are sure they are not coins. For that, we dilate the result. Dilation increases the object boundary to the background. This way, we can make sure whatever region in the background in result is really a background, since the boundary region is removed. See the image below.

![image](https://docs.opencv.org/5.x/water_fgbg.jpg)

The remaining regions are those which we don't have any idea, whether it is coins or background. The watershed algorithm should find it. These areas are normally around the boundaries of coins where foreground and background meet (or even two different coins meet). We call it the border. It can be obtained by subtracting the `sure_fg` area from the `sure_bg` area.

```python
# Noise removal
kernel = np.ones((3,3), np.uint8)
opening = cv.morphologyEx(thresh, cv.MORPH_OPEN, kernel, iterations=2)

# Sure background area
sure_bg = cv.dilate(opening, kernel, iterations=3)

# Finding sure foreground area
dist_transform = cv.distanceTransform(opening, cv.DIST_L2, 5)
ret, sure_fg = cv.threshold(dist_transform, 0.7 * dist_transform.max(), 255, 0)

# Finding unknown region
sure_fg = np.uint8(sure_fg)
unknown = cv.subtract(sure_bg, sure_fg)
```

**Result:**

In the thresholded image, we get some regions of coins which we are sure of coins and they are detached now. (In some cases, you may be interested in only foreground segmentation, not in separating the mutually touching objects. In that case, you need not use distance transform, just erosion is sufficient. Erosion is just another method to extract sure foreground area, that's all.)

![image](https://docs.opencv.org/5.x/water_dt.jpg)

Now we know for sure which are the regions of coins, which are background, and all. So we create a marker (it is an array of the same size as that of the original image, but with int32 datatype) and label the regions inside it. The regions we know for sure (whether foreground or background) are labeled with any positive integers, but different integers, and the area we don't know for sure are just left as zero. For this, we use `cv.connectedComponents()`. It labels the background of the image with 0, then other objects are labeled with integers starting from 1.

But we know that if the background is marked with 0, the watershed will consider it as an unknown area. So we want to mark it with a different integer. Instead, we will mark the unknown region, defined by `unknown`, with 0.

```python
# Marker labelling
ret, markers = cv.connectedComponents(sure_fg)

# Add one to all labels so that sure background is not 0, but 1
markers = markers + 1

# Now, mark the region of unknown with zero
markers[unknown == 255] = 0
```

See the result shown in the JET colormap. The dark blue region shows the unknown region. Sure coins are colored with different values. The remaining area which are sure background are shown in lighter blue compared to the unknown region.

![image](https://docs.opencv.org/5.x/water_marker.jpg)

Now our marker is ready. It is time for the final step, apply watershed. Then the marker image will be modified. The boundary region will be marked with -1.

```python
markers = cv.watershed(img, markers)
img[markers == -1] = [255, 0, 0]
```

See the result below. For some coins, the region where they touch is segmented properly and for some, they are not.

![image](https://docs.opencv.org/5.x/water_result.jpg)

```

Craeated by shyama7004 with the help of openCv Docs:)
```

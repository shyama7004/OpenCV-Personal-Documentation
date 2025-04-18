## Feature Detection and Description

### Feature Matching

### Goal

In this chapter, we will see how to match features in one image with others. We will use the Brute-Force matcher and FLANN Matcher in OpenCV.

### Basics of Brute-Force Matcher

Brute-Force matcher is simple. It takes the descriptor of one feature in the first set and matches it with all other features in the second set using some distance calculation. The closest one is returned.

For BF matcher, first, we have to create the `BFMatcher` object using `cv.BFMatcher()`. It takes two optional parameters. The first one is `normType`, which specifies the distance measurement to be used. By default, it is `cv.NORM_L2`, which is good for SIFT, SURF, etc. (`cv.NORM_L1` is also available). For binary string-based descriptors like ORB, BRIEF, BRISK, etc., `cv.NORM_HAMMING` should be used, which uses Hamming distance as measurement. If ORB is using `WTA_K == 3` or `4`, `cv.NORM_HAMMING2` should be used.

The second parameter is a boolean variable, `crossCheck`, which is `False` by default. If it is `True`, the matcher returns only those matches with value `(i,j)` such that the `i-th` descriptor in set A has the `j-th` descriptor in set B as the best match and vice-versa. That is, the two features in both sets should match each other. This provides consistent results and is a good alternative to the ratio test proposed by D. Lowe in the SIFT paper.

Once it is created, two important methods are `BFMatcher.match()` and `BFMatcher.knnMatch()`. The first one returns the best match, while the second method returns `k` best matches where `k` is specified by the user. It may be useful when we need to do additional work on that.

Like we used `cv.drawKeypoints()` to draw keypoints, `cv.drawMatches()` helps us draw the matches. It stacks two images horizontally and draws lines from the first image to the second image showing the best matches. There is also `cv.drawMatchesKnn()` which draws all the `k` best matches. If `k=2`, it will draw two match-lines for each keypoint. So we have to pass a mask if we want to selectively draw it.

Let's see an example for both SIFT and ORB (Both use different distance measurements).

### Brute-Force Matching with ORB Descriptors

Here, we will see a simple example of how to match features between two images. In this case, we have a `queryImage` and a `trainImage`. We will try to find the `queryImage` in `trainImage` using feature matching. (The images are `/samples/data/box.png` and `/samples/data/box_in_scene.png`).

We are using ORB descriptors to match features. Let's start with loading images, finding descriptors, etc.

```python
import numpy as np
import cv2 as cv
import matplotlib.pyplot as plt

img1 = cv.imread('box.png', cv.IMREAD_GRAYSCALE)          # queryImage
img2 = cv.imread('box_in_scene.png', cv.IMREAD_GRAYSCALE) # trainImage

# Initiate ORB detector
orb = cv.ORB_create()

# find the keypoints and descriptors with ORB
kp1, des1 = orb.detectAndCompute(img1, None)
kp2, des2 = orb.detectAndCompute(img2, None)
```

Next, we create a `BFMatcher` object with distance measurement `cv.NORM_HAMMING` (since we are using ORB) and `crossCheck` is switched on for better results. Then we use `Matcher.match()` method to get the best matches in two images. We sort them in ascending order of their distances so that the best matches (with low distance) come to the front. Then we draw only the first 10 matches (Just for the sake of visibility. You can increase it as you like).

```python
# create BFMatcher object
bf = cv.BFMatcher(cv.NORM_HAMMING, crossCheck=True)

# Match descriptors.
matches = bf.match(des1, des2)

# Sort them in the order of their distance.
matches = sorted(matches, key=lambda x: x.distance)

# Draw first 10 matches.
img3 = cv.drawMatches(img1, kp1, img2, kp2, matches[:10], None, flags=cv.DrawMatchesFlags_NOT_DRAW_SINGLE_POINTS)

plt.imshow(img3), plt.show()
```

Below is the result:

![Brute-Force Matching with ORB Descriptors](https://docs.opencv.org/5.x/matcher_result1.jpg)

#### What is this Matcher Object?

The result of `matches = bf.match(des1, des2)` line is a list of `DMatch` objects. This `DMatch` object has the following attributes:

- `DMatch.distance` - Distance between descriptors. The lower, the better it is.
- `DMatch.trainIdx` - Index of the descriptor in train descriptors.
- `DMatch.queryIdx` - Index of the descriptor in query descriptors.
- `DMatch.imgIdx` - Index of the train image.

### Brute-Force Matching with SIFT Descriptors and Ratio Test

This time, we will use `BFMatcher.knnMatch()` to get `k` best matches. In this example, we will take `k=2` so that we can apply the ratio test explained by D. Lowe in his paper.

```python
import numpy as np
import cv2 as cv
import matplotlib.pyplot as plt

img1 = cv.imread('box.png', cv.IMREAD_GRAYSCALE)          # queryImage
img2 = cv.imread('box_in_scene.png', cv.IMREAD_GRAYSCALE) # trainImage

# Initiate SIFT detector
sift = cv.SIFT_create()

# find the keypoints and descriptors with SIFT
kp1, des1 = sift.detectAndCompute(img1, None)
kp2, des2 = sift.detectAndCompute(img2, None)

# BFMatcher with default params
bf = cv.BFMatcher()
matches = bf.knnMatch(des1, des2, k=2)

# Apply ratio test
good = []
for m, n in matches:
    if m.distance < 0.75 * n.distance:
        good.append([m])

# cv.drawMatchesKnn expects list of lists as matches.
img3 = cv.drawMatchesKnn(img1, kp1, img2, kp2, good, None, flags=cv.DrawMatchesFlags_NOT_DRAW_SINGLE_POINTS)

plt.imshow(img3), plt.show()
```

See the result below:

![Brute-Force Matching with SIFT Descriptors](https://docs.opencv.org/5.x/matcher_result2.jpg)

### FLANN based Matcher

FLANN stands for Fast Library for Approximate Nearest Neighbors. It contains a collection of algorithms optimized for fast nearest neighbor search in large datasets and for high dimensional features. It works faster than `BFMatcher` for large datasets. We will see the second example with FLANN based matcher.

For FLANN based matcher, we need to pass two dictionaries which specify the algorithm to be used, its related parameters, etc. The first one is `IndexParams`. For various algorithms, the information to be passed is explained in FLANN docs. As a summary, for algorithms like SIFT, SURF, etc., you can pass the following:

```python
FLANN_INDEX_KDTREE = 1
index_params = dict(algorithm=FLANN_INDEX_KDTREE, trees=5)
```

While using ORB, you can pass the following. The commented values are recommended as per the docs, but they didn't provide the required results in some cases. Other values worked fine:

```python
FLANN_INDEX_LSH = 6
index_params = dict(algorithm=FLANN_INDEX_LSH,
                    table_number=6,  # 12
                    key_size=12,     # 20
                    multi_probe_level=1)  # 2
```

The second dictionary is the `SearchParams`. It specifies the number of times the trees in the index should be recursively traversed. Higher values give better precision, but also take more time. If you want to change the value, pass `search_params = dict(checks=100)`.

With this information, we are good to go.

```python
import numpy as np
import cv2 as cv
import matplotlib.pyplot as plt

img1 = cv.imread('box.png', cv.IMREAD_GRAYSCALE)          # queryImage
img2 = cv.imread('box_in_scene.png', cv.IMREAD_GRAYSCALE) # trainImage

# Initiate SIFT detector
sift = cv.SIFT_create()

# find the keypoints and descriptors with SIFT
kp1, des1 = sift.detectAndCompute(img1, None)
kp2, des2 = sift.detectAndCompute(img2, None)

# FLANN parameters
FLANN_INDEX_KDTREE = 1
index_params = dict(algorithm=FLANN_INDEX_KDTREE, trees=5)
search_params = dict(checks=50)  # or pass an empty dictionary

flann = cv.FlannBasedMatcher(index_params, search_params)

matches = flann.knnMatch(des1, des2, k=2)

# Need to draw only good matches, so create a mask
matchesMask = [[0, 0] for i in range(len(matches))]

# ratio test as per Lowe's paper
for i, (m, n) in enumerate(matches):


    if m.distance < 0.7 * n.distance:
        matchesMask[i] = [1, 0]

draw_params = dict(matchColor=(0, 255, 0),
                   singlePointColor=(255, 0, 0),
                   matchesMask=matchesMask,
                   flags=cv.DrawMatchesFlags_DEFAULT)

img3 = cv.drawMatchesKnn(img1, kp1, img2, kp2, matches, None, **draw_params)

plt.imshow(img3), plt.show()
```

See the result below:

![FLANN based Matcher](https://docs.opencv.org/5.x/matcher_flann.jpg)

### Note

To use `cv.ORB()` and `cv.SIFT()`, make sure you have installed OpenCV with extra modules. Otherwise, it won't work.

```markdown
Created by shyama7004 with help of Opencv Docs :)
```

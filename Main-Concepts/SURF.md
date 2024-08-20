# Introduction to SURF (Speeded-Up Robust Features)

## Goal
In this chapter:
- We will see the basics of SURF.
- We will see SURF functionalities in OpenCV.

## Theory
In the last chapter, we saw SIFT for keypoint detection and description. However, it was comparatively slow, and there was a need for a more speeded-up version. In 2006, Bay, H., Tuytelaars, T., and Van Gool, L. published a paper titled "SURF: Speeded Up Robust Features," introducing a new algorithm called SURF. As the name suggests, it is a speeded-up version of SIFT.

In SIFT, Lowe approximated the Laplacian of Gaussian with the Difference of Gaussian for finding scale-space. SURF goes further by approximating LoG with a Box Filter. The image below demonstrates this approximation. One big advantage is that convolution with a box filter can be easily calculated using integral images, and it can be done in parallel for different scales. Additionally, SURF relies on the determinant of the Hessian matrix for both scale and location.

<div align ="center"><img src ="https://docs.opencv.org/4.x/surf_boxfilter.jpg"></div>

For orientation assignment, SURF uses wavelet responses in the horizontal and vertical direction for a neighborhood of size 6s. Adequate Gaussian weights are also applied to it. Then, they are plotted in a space as shown in the image below. The dominant orientation is estimated by calculating the sum of all responses within a sliding orientation window of 60 degrees. The wavelet response can be found using integral images very easily at any scale. For many applications, rotation invariance is not required, so finding this orientation is unnecessary, which speeds up the process. SURF provides this functionality, called Upright-SURF or U-SURF. It improves speed and is robust up to some point. OpenCV supports both, depending on the flag `upright`. If it is 0, orientation is calculated; if it is 1, orientation is not calculated, making it faster.

<div align ="center"><img src = "https://docs.opencv.org/4.x/surf_orientation.jpg"></div>

For feature description, SURF uses wavelet responses in the horizontal and vertical direction (again, the use of integral images makes things easier). A neighborhood of size 20s x 20s is taken around the keypoint where `s` is the size. It is divided into 4x4 subregions. For each subregion, horizontal and vertical wavelet responses are taken and a vector is formed like this: ![Vector](image). This vector gives the SURF feature descriptor with a total of 64 dimensions. Lower dimensions result in higher speed of computation and matching, but provide better distinctiveness of features.

For more distinctiveness, SURF feature descriptor has an extended 128-dimension version. The sums of ![Vector](image) and ![Vector](image) are computed separately for ![Vector](image) and ![Vector](image). Similarly, the sums of ![Vector](image) and ![Vector](image) are split according to the sign of ![Vector](image), doubling the number of features. It doesn't add much computational complexity. OpenCV supports both by setting the value of the flag `extended` with 0 and 1 for 64-dim and 128-dim respectively (default is 128-dim).

Another important improvement is the use of the sign of the Laplacian (trace of the Hessian Matrix) for the underlying interest point. It adds no computation cost since it is already computed during detection. The sign of the Laplacian distinguishes bright blobs on dark backgrounds from the reverse situation. In the matching stage, we only compare features if they have the same type of contrast (as shown in the image below). This minimal information allows for faster matching without reducing the descriptor's performance.

<div align ="center"><img src = "https://docs.opencv.org/4.x/surf_matching.jpg"></div>

In short, SURF adds a lot of features to improve speed at every step. Analysis shows it is three times faster than SIFT while performance is comparable. SURF is good at handling images with blurring and rotation, but not good at handling viewpoint change and illumination change.

## SURF in OpenCV
OpenCV provides SURF functionalities just like SIFT. You initiate a SURF object with some optional conditions like 64/128-dim descriptors, Upright/Normal SURF, etc. All the details are well explained in the [documentation](https://docs.opencv.org/). Then, as we did in SIFT, we can use `SURF.detect()`, `SURF.compute()`, etc., for finding keypoints and descriptors.

First, we will see a simple demo on how to find SURF keypoints and descriptors and draw them. All examples are shown in the Python terminal since it is just the same as SIFT.

```python
>>> img = cv.imread('fly.png', cv.IMREAD_GRAYSCALE)

# Create SURF object. You can specify params here or later.
# Here I set Hessian Threshold to 400
>>> surf = cv.xfeatures2d.SURF_create(400)

# Find keypoints and descriptors directly
>>> kp, des = surf.detectAndCompute(img, None)

>>> len(kp)
699
```

1199 keypoints are too many to show in a picture. We reduce it to around 50 to draw on an image. While matching, we may need all those features, but not now. So, we increase the Hessian Threshold.

```python
# Check the present Hessian threshold
>>> print(surf.getHessianThreshold())
400.0

# We set it to some 50000. Remember, it is just for representing in the picture.
# In actual cases, it is better to have a value between 300-500
>>> surf.setHessianThreshold(50000)

# Again compute keypoints and check its number
>>> kp, des = surf.detectAndCompute(img, None)

>>> print(len(kp))
47
```

It is less than 50. Let's draw it on the image.

```python
>>> img2 = cv.drawKeypoints(img, kp, None, (255, 0, 0), 4)

>>> plt.imshow(img2), plt.show()
```

See the result below. You can see that SURF is more like a blob detector. It detects the white blobs on the wings of the butterfly. You can test it with other images.

<div align ="center"><img src ="https://docs.opencv.org/4.x/surf_kp1.jpg"></div>

Now, I want to apply U-SURF, so that it won't find the orientation.

```python
# Check the upright flag, if it is False, set it to True
>>> print(surf.getUpright())
False

>>> surf.setUpright(True)

# Recompute the feature points and draw them
>>> kp = surf.detect(img, None)
>>> img2 = cv.drawKeypoints(img, kp, None, (255, 0, 0), 4)

>>> plt.imshow(img2), plt.show()
```

See the results below. All the orientations are shown in the same direction. It is faster than before. If you are working on cases where orientation is not a problem (like panorama stitching), this is better.

<div align ="center"><img src ="https://docs.opencv.org/4.x/surf_kp2.jpg"></div>

Finally, we check the descriptor size and change it to 128 if it is only 64-dim.

```python
# Find the size of the descriptor
>>> print(surf.descriptorSize())
64

# That means the flag "extended" is False.
>>> surf.getExtended()
False

# So, we make it True to get 128-dim descriptors.
>>> surf.setExtended(True)
>>> kp, des = surf.detectAndCompute(img, None)
>>> print(surf.descriptorSize())
128
>>> print(des.shape)
(47, 128)
```

# Introduction to SURF (Speeded-Up Robust Features)

## Goal
In this chapter:
- We will see the basics of SURF.
- We will see SURF functionalities in OpenCV.

## Theory
In the last chapter, we saw SIFT for keypoint detection and description. However, it was comparatively slow, and there was a need for a more speeded-up version. In 2006, Bay, H., Tuytelaars, T., and Van Gool, L. published a paper titled "SURF: Speeded Up Robust Features," introducing a new algorithm called SURF. As the name suggests, it is a speeded-up version of SIFT.

In SIFT, Lowe approximated the Laplacian of Gaussian with the Difference of Gaussian for finding scale-space. SURF goes further by approximating LoG with a Box Filter. The image below demonstrates this approximation. One big advantage is that convolution with a box filter can be easily calculated using integral images, and it can be done in parallel for different scales. Additionally, SURF relies on the determinant of the Hessian matrix for both scale and location.

<details>
<summary>Click here to see Hesian Matrix</summary>

<img src ="https://imgs.search.brave.com/sY6MYyB49xSyOx4dODQeR6Dx8AZW8Jkt0lNyxDFGNj8/rs:fit:860:0:0:0/g:ce/aHR0cHM6Ly9jb250/ZW50LmNkbnR3cmsu/Y29tL2ZpbGVzL2FI/VmlQVEV4T0RZeU5T/WmpiV1E5YVhSbGJX/VmthWFJ2Y21sdFlX/ZGxKbVpwYkdWdVlX/MWxQV2wwWlcxbFpH/bDBiM0pwYldGblpW/ODJNemt3TXpJM05X/RTBPRGszTGtwUVJ5/WjJaWEp6YVc5dVBU/QXdNREFtYzJsblBU/ZzRZVEpqTm1NMFky/UTVNMk00TlRVd09X/WTVNVEEyWkRnME5E/SmhNMkl5">

</details>

<div align ="center"><img src ="https://docs.opencv.org/4.x/surf_boxfilter.jpg"></div>

For orientation assignment, SURF uses wavelet responses in the horizontal and vertical direction for a neighborhood of size 6s.
<details>
  <summary>Click here to know about wavelet responses</summary>
 

Wavelet responses refer to the output of a wavelet transform, which is a mathematical technique used to decompose a signal into its constituent frequency components at different scales. In essence, wavelet responses provide a multiscale representation of a signal, allowing for the analysis of both time and frequency domains simultaneously.

Key Characteristics

Multiscale: Wavelet responses offer a hierarchical representation of a signal, with each scale corresponding to a specific frequency range.
Time-frequency: Wavelet responses capture both time and frequency information, enabling the analysis of non-stationary signals and the detection of localized events.
Scalability: Wavelet responses can be computed at varying scales, allowing for the selection of the most relevant frequency ranges for a particular application.
Applications

Signal Processing: Wavelet responses are used in signal processing for tasks such as denoising, compression, and feature extraction.
Non-destructive Testing: Wavelet responses are employed in non-destructive testing (NDT) methods, like ultrasonic testing, to analyze the reflections of incident wavelets on material discontinuities.
Structural Health Monitoring: Wavelet responses are utilized in structural health monitoring (SHM) to detect damage and anomalies in structures by analyzing Lamb wave propagation.
Notable Properties

Shift and Scaling Coefficients: The coefficients of a wavelet filter bank, known as shift and scaling coefficients, determine the wavelet’s time-frequency resolution and scalability.
Uncertainty Principle: The continuous wavelet transform (CWT) is subject to the uncertainty principle, which limits the simultaneous resolution of time and frequency.
Orthogonality: Orthogonal wavelets, defined by a low-pass finite impulse response (FIR) filter, ensure efficient computation and optimal signal representation.
By leveraging these properties and characteristics, wavelet responses have become a powerful tool in various fields, enabling the analysis and processing of complex signals with unprecedented flexibility and accuracy.
</details>

- Gaussian weights are applied to the data.
- The results are plotted as shown in the image.
- The dominant direction is found by summing all responses within a 60-degree sliding window.
- A "60-degree sliding window" means looking at a section of angles that covers 60 degrees at a time. You then move this 60-degree section across all possible angles to find the       direction with the strongest signal or feature.
- The wavelet response can be quickly found at any scale using integral images.
- Integral images are a way to quickly calculate the sum of pixel values in a rectangular area of an image. They speed up processes like detecting features or patterns in an image by allowing these sums to be computed very efficiently, regardless of the size of the area.
- In some cases, finding this orientation isn't necessary, which speeds up the process.
- SURF offers a faster version called Upright-SURF (U-SURF) for these situations.
- In OpenCV, you can control this with the `upright` flag:
  - If `upright = 0`, orientation is calculated.
  - If `upright = 1`, orientation is not calculated, making it faster.

<div align ="center"><img src = "https://docs.opencv.org/4.x/surf_orientation.jpg"></div>

- **Feature Description in SURF**:
  - SURF (Speeded-Up Robust Features) describes features by analyzing wavelet responses in both horizontal and vertical directions.
  - A neighborhood around the keypoint is taken, typically of size 20x20 pixels.
  - This area is divided into 4x4 smaller regions, making a total of 16 subregions.
  - For each subregion:
    - Wavelet responses in both the horizontal (`dx`) and vertical (`dy`) directions are calculated.
    - These responses are then combined into a vector that captures the characteristics of the keypoint.
    - This vector has 64 dimensions (or values), providing a balance between speed and accuracy in feature matching.
  
- **Extended Feature Description**:
  - For more detailed feature descriptions, SURF offers an extended version with 128 dimensions.
  - This is done by separately calculating the sums of the wavelet responses (`dx` and `|dx|`) based on the sign of the vertical response (`dy`):
    - If `dy < 0`, one set of sums is computed.
    - If `dy ≥ 0`, another set of sums is computed.
  - This doubles the number of features in the vector, providing better distinctiveness while still maintaining reasonable computation time.
  - OpenCV allows you to choose between the standard 64-dimensional version and the extended 128-dimensional version using the `extended` flag.

- **Sign of Laplacian for Matching**:
  - Another important aspect of SURF is the use of the "sign of Laplacian" (related to the trace of the Hessian matrix) during feature detection.
  - The sign of the Laplacian helps distinguish between bright blobs on dark backgrounds and dark blobs on bright backgrounds.
  - This feature is already computed during detection, so it adds no extra computation cost.
  - During matching, only features with the same Laplacian sign are compared, which speeds up the matching process without losing accuracy.

<details>
<summary>Click here to know more about Laplacian usage</summary>
The "Sign of Laplacian" is a concept used in image processing and feature detection, particularly in algorithms like SURF. Here's a breakdown of what it means:

- **Laplacian**: 
  - The Laplacian is a mathematical operation that calculates the second derivatives of an image. It helps detect edges or regions in an image where the intensity changes rapidly.
  - When applied to an image, the Laplacian can highlight areas that are either very bright or very dark compared to their surroundings.

- **Sign of Laplacian**:
  - The "sign" of the Laplacian simply refers to whether the value of the Laplacian is positive or negative:
    - **Positive Sign**: Indicates that the area is a **bright region** (blob) on a darker background.
    - **Negative Sign**: Indicates that the area is a **dark region** (blob) on a brighter background.
  
- **Usage in SURF**:
  - SURF uses the sign of the Laplacian to help distinguish between different types of features. 
  - When matching features between images, SURF only compares features that have the same sign of the Laplacian. This ensures that only similar types of features (e.g., bright regions on dark backgrounds) are compared, which improves the accuracy of the matching process.
  - The sign of the Laplacian is determined during the initial detection phase, so using it for matching doesn't add any extra computational cost.
</details>

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

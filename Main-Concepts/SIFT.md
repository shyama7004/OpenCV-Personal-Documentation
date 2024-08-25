# Introduction to SIFT (Scale-Invariant Feature Transform)

## Goal
- **Objective**: 
  - Understand the concepts and workings of the SIFT algorithm.
  - Learn how to find SIFT keypoints and descriptors in images.

## Theory
### Overview:
- **Rotation-Invariant Detectors**: In previous chapters, detectors like Harris Corner Detector were discussed. These detectors can identify the same corners in an image even if it is rotated because corners remain corners regardless of the rotation. However, these detectors are not scale-invariant.
  
- **Scale Problem**: 
  - When an image is scaled (e.g., zoomed in or out), a corner in the original image might not remain a corner after scaling. For instance, a small corner in a low-resolution image might appear flat or less distinct when the image is magnified.

  ![Corners in different scales](https://docs.opencv.org/4.x/sift_scale_invariant.jpg)

  *Image: Demonstration of a corner that changes appearance with scaling.*

- **SIFT Algorithm**: 
  - To address the issue of scale, David Lowe from the University of British Columbia introduced the Scale-Invariant Feature Transform (SIFT) in 2004. This algorithm detects keypoints and computes descriptors that are invariant to both rotation and scaling. 
  - **Keypoints** are distinctive points in an image, such as corners, edges, or blobs, that are used for identifying and describing important features.
  - **Descriptors** are vectors that describe the appearance of keypoints, making it possible to match them across different images.

### Steps in SIFT Algorithm:
1. **Scale-space Extrema Detection**:
   - **Challenge**: Keypoints must be detected at different scales. A corner in a small window might not be visible or might appear different in a larger window, especially if the image is scaled.
   - **Solution**: Use *Scale-Space Filtering* to detect keypoints at multiple scales.
   - **Scale-Space Filtering**: 
     - The Laplacian of Gaussian (LoG) is applied to the image at various scales (different values of σ). This process acts as a blob detector, identifying regions that look like blobs at different sizes.
     - LoG can detect both small and large blobs depending on the value of σ, with σ acting as a scaling factor.
     - For instance, in the image above, a small Gaussian kernel (low σ) detects small corners, while a large Gaussian kernel (high σ) is needed for larger corners.

For more information about LoG [Click Here](https://homepages.inf.ed.ac.uk/rbf/HIPR2/log.htm)
   
   - **Difference of Gaussians (DoG)**: 
     - Computing LoG directly is computationally expensive. Instead, SIFT uses the Difference of Gaussians (DoG), which is an efficient approximation of LoG.
     - DoG is calculated by subtracting two Gaussian-blurred versions of the image, each with a different σ. This process is repeated across different scales (octaves) of the image.

     ![Difference of Gaussians](https://docs.opencv.org/4.x/sift_dog.jpg)

     *Image: Illustration of the Difference of Gaussians across scales.*

   - **Local Extrema Detection**:
     - After computing DoG, the next step is to find local extrema in both scale and space. This means identifying pixels that are either maxima or minima compared to their neighbors at different scales.
     - A pixel is compared with its 8 neighbors in the same image scale and with 9 neighbors in the scales above and below. If it is the highest or lowest value in this neighborhood, it is marked as a potential keypoint.

     ![Local Extrema](https://docs.opencv.org/4.x/sift_local_extrema.jpg)

     *Image: Finding local extrema by comparing a pixel with its neighbors across scales.*

   - **Empirical Parameters**:
     - To ensure robust detection of keypoints, certain parameters are empirically set based on research:
       - Number of octaves = 4
       - Number of scale levels = 5
       - Initial σ and scaling factor k are optimized for best performance.

2. **Keypoint Localization**:
   - **Purpose**: After identifying potential keypoints, their positions need to be refined for accuracy.
   - **Taylor Series Expansion**: 
     - A more precise location of the keypoint is found by fitting a 3D quadratic function (using Taylor series expansion) to the scale-space around the detected keypoint.
     - If the intensity difference at the keypoint location is below a certain threshold (0.03 as per the original paper), it is discarded. This threshold is called `contrastThreshold` in OpenCV.
   <details>
     <summary>Click here to see the taylor series</summary>
     <div align ="center"><img src="https://machinelearningmastery.com/wp-content/uploads/2021/07/tayloreq2-1024x216.png"></div>
   </details>

   - **Edge Response Removal**:
     - The DoG function often has strong responses along edges. However, keypoints detected on edges are not as stable as those on corners or blobs.
     - To remove these edge points, the algorithm uses a concept similar to the Harris Corner Detector. It computes the principal curvatures of the 2x2 Hessian matrix (H) at the keypoint.

<details>
  <summary>Click to see explanation of above point</summary>

  - The algorithm removes edge points using a technique similar to the Harris Corner Detector.
- It computes the **Hessian matrix** at each keypoint to capture the image's intensity curvature.
- The **principal curvatures** (eigenvalues of the Hessian matrix) are calculated at the keypoint.
- Based on these curvatures, the algorithm determines whether the keypoint is on an edge or a corner.
- If the curvatures suggest an edge, the keypoint is discarded.
- This process ensures that only strong corner points are retained.

</details>

  - If the ratio of the principal curvatures (eigenvalues) exceeds a threshold (`edgeThreshold`), the keypoint is discarded.
  - This step ensures that only strong, stable keypoints remain.

3. **Orientation Assignment**:
   - **Purpose**: To make the keypoints invariant to image rotation, an orientation is assigned to each keypoint.
   - **Process**:
     - A neighborhood around each keypoint is taken, and the gradient magnitude and direction of each pixel in this neighborhood are calculated.
     - An orientation histogram with 36 bins (covering 360 degrees) is created, weighted by the gradient magnitudes and a Gaussian window with σ proportional to the scale of the keypoint.
     - The highest point in the histogram shows the main direction of the keypoint.
     - If there are other points in the histogram that are at least 80% as high as the main peak, extra orientations are assigned.
     - This means there could be multiple keypoints with the same position and size but different directions.
   
   - **Result**: The keypoints are now rotation-invariant, meaning they can be detected and matched even if the image is rotated.

4. **Keypoint Descriptor**:
   - **Purpose**: The next step is to describe the keypoints in a way that allows them to be matched across different images.
   - **Process**:
     - A 16x16 neighborhood around the keypoint is selected.
     - This neighborhood is divided into 16 smaller 4x4 sub-blocks.
     - For each sub-block, an 8-bin orientation histogram is created, similar to the orientation assignment step but on a smaller scale.
     - The histograms from all 16 sub-blocks are concatenated into a 128-dimensional vector (16 sub-blocks x 8 bins = 128), which forms the keypoint descriptor.
     - This descriptor is designed to be robust against variations in lighting, orientation, and other distortions.

5. **Keypoint Matching**:
   - **Purpose**: Finally, keypoints from different images are matched to identify corresponding features.
   - **Process**:
     - Each keypoint descriptor is compared to all others in a second image to find the nearest neighbor based on Euclidean distance.
     - To avoid false matches, a ratio test is applied: the distance to the closest match is compared to the distance to the second-closest match. If the ratio is greater than 0.8, the match is rejected.

<div align ="center"><h3>Ratio Test</h3></div>

<div align =center><img src ="https://i.ytimg.com/vi/MMmFnH1ZHyM/maxresdefault.jpg" width =300 ></div>

  - This test helps eliminate about 90% of false matches while retaining 95% of correct matches, making the matching process more reliable.

   - **Result**: After matching keypoints, the images can be aligned, stitched, or analyzed for common features.

### Summary:
The SIFT algorithm is a powerful tool for feature detection and matching, capable of handling variations in scale, rotation, and lighting. Understanding the key steps—scale-space extrema detection, keypoint localization, orientation assignment, keypoint descriptor creation, and keypoint matching—provides insight into how SIFT achieves robust performance in various computer vision tasks.

## SIFT in OpenCV
### Using SIFT in OpenCV:
- **Introduction**: The SIFT functionality, initially only available in the `opencv_contrib` repository, is now included in the main OpenCV repository after the patent expired in 2020.

### Keypoint Detection:
- **Creating a SIFT Object**:
  - First, a SIFT object needs to be constructed in OpenCV. This object can then be used to detect keypoints in an image.

  ```python
  import cv2 as cv

  # Load the image
  img = cv.imread('home.jpg')
  gray = cv.cvtColor(img, cv.COLOR_BGR2GRAY)

  # Create SIFT object
  sift = cv.SIFT_create()

  # Detect keypoints
  kp = sift.detect(gray, None)

  # Draw keypoints on the image
  img = cv.drawKeypoints(gray, kp, img)

  # Save the image with keypoints drawn
  cv.imwrite('sift_keypoints.jpg', img)
  ```

- **Keypoint Structure**:
  - Each detected keypoint is represented by a special structure containing attributes like:
    - **(x, y) Coordinates**: The location of the keypoint in the image.
    - **Size**: The size of the keypoint's neighborhood.
    - **Angle**: The orientation of the keypoint.
    - **Response**: A measure of the keypoint's strength or importance.

For more code explanation : [Click here](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/11.3.md)

- **Drawing Keypoints**:
  - The `cv.drawKeyPoints()` function can draw small circles at the locations of key

points. By passing the flag `cv.DRAW_MATCHES_FLAGS_DRAW_RICH_KEYPOINTS`, you can draw circles proportional to the keypoint size and show their orientations.

  ```python
  img = cv.drawKeypoints(gray, kp, img, flags=cv.DRAW_MATCHES_FLAGS_DRAW_RICH_KEYPOINTS)
  cv.imwrite('sift_keypoints.jpg', img)
  ```

  ![SIFT Keypoints](https://docs.opencv.org/4.x/sift_keypoints.jpg)

  *Image: Keypoints detected and drawn with orientations.*

### Keypoint Descriptor Calculation:
- **Two Methods to Calculate Descriptors**:
  1. **Using Pre-detected Keypoints**:
     - If you already have detected keypoints, you can compute descriptors from these keypoints.

     ```python
     kp, des = sift.compute(gray, kp)
     ```

  2. **Detecting and Computing Simultaneously**:
     - Alternatively, you can detect keypoints and compute their descriptors in a single step.

     ```python
     sift = cv.SIFT_create()
     kp, des = sift.detectAndCompute(gray, None)
     ```

- **Output**:
  - `kp`: A list of keypoints.
  - `des`: A numpy array of shape `(Number of Keypoints, 128)` representing the descriptors.

### Next Steps:
- **Matching Keypoints**:
  - Once you have keypoints and descriptors from multiple images, the next step is to match these keypoints across images. This process will be covered in the following chapters.

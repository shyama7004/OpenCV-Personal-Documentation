## Image Blending using Pyramids

To blend images using image pyramids in OpenCV, you typically follow these steps:

1. **Generate Gaussian Pyramids**: Create Gaussian pyramids for each input image. This involves repeatedly downsampling the image using `cv2.pyrDown()` to create a series of progressively smaller images.

2. **Generate Laplacian Pyramids**: From the Gaussian pyramids, generate Laplacian pyramids. Each level of the Laplacian pyramid is formed by subtracting the upsampled version of the next level in the Gaussian pyramid from the current level.

3. **Blend the Images**: Use a mask to blend the Laplacian pyramids of the two images. The mask can be a [binary or gradient mask](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/2.4.md) that determines how much of each image is used in different regions.

4. **Reconstruct the Image**: Reconstruct the final blended image by upsampling and adding the blended Laplacian pyramid levels.

Here is an example code that demonstrates this process:

```py
import cv2
import numpy as np

def blend_images(img1, img2):
    # Generate Gaussian pyramids for both images
    G1 = img1.copy()
    G2 = img2.copy()
    gp1 = [G1]
    gp2 = [G2]
    
    for _ in range(6):
        G1 = cv2.pyrDown(G1)
        G2 = cv2.pyrDown(G2)
        gp1.append(G1)
        gp2.append(G2)
    
    # Generate Laplacian pyramids for both images
    lp1 = [gp1[-1]]
    lp2 = [gp2[-1]]
    
    for i in range(5, 0, -1):
        L1 = cv2.subtract(gp1[i-1], cv2.pyrUp(gp1[i]))
        L2 = cv2.subtract(gp2[i-1], cv2.pyrUp(gp2[i]))
        lp1.append(L1)
        lp2.append(L2)
    
    # Blend the Laplacian pyramids
    LS = []
    for l1, l2 in zip(lp1, lp2):
        rows, cols, dpt = l1.shape
        ls = np.hstack((l1[:, :cols // 2], l2[:, cols // 2:]))
        LS.append(ls)
    
    # Reconstruct the image
    ls_ = LS[0]
    for i in range(1, 6):
        ls_ = cv2.pyrUp(ls_)
        ls_ = cv2.add(ls_, LS[i])
    
    return ls_

# Read the images
img1 = cv2.imread('apple.jpg')
img2 = cv2.imread('orange.jpg')

# Blend the images
blended_image = blend_images(img1, img2)

# Display the result
cv2.imshow('Blended Image', blended_image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
## Step by Step Explanation

### Imports
```python
import cv2
import numpy as np
```
- **`cv2`**: This is the OpenCV library, which provides functions for image processing.
- **`np`**: This is the NumPy library, which provides support for large, multi-dimensional arrays and matrices.

### Blending Function Definition
```python
def blend_images(img1, img2):
```
- **`blend_images`**: This is a function that takes two images as input and returns a blended image using the pyramid blending technique.

### Generate Gaussian Pyramids
```python
    # Generate Gaussian pyramids for both images
    G1 = img1.copy()
    G2 = img2.copy()
    gp1 = [G1]
    gp2 = [G2]
    
    for _ in range(6):
        G1 = cv2.pyrDown(G1)
        G2 = cv2.pyrDown(G2)
        gp1.append(G1)
        gp2.append(G2)
```
- **`G1` and `G2`**: Copies of the input images `img1` and `img2`.
- **`gp1` and `gp2`**: Lists to store the Gaussian pyramids of `img1` and `img2`.
- **Loop**: This loop creates 6 levels of the Gaussian pyramid for each image by repeatedly applying the `cv2.pyrDown` function, which reduces the image size by a factor of 2.

### Generate Laplacian Pyramids
```python
    # Generate Laplacian pyramids for both images
    lp1 = [gp1[-1]]
    lp2 = [gp2[-1]]
    
    for i in range(5, 0, -1):
        L1 = cv2.subtract(gp1[i-1], cv2.pyrUp(gp1[i]))
        L2 = cv2.subtract(gp2[i-1], cv2.pyrUp(gp2[i]))
        lp1.append(L1)
        lp2.append(L2)
```
- **`lp1` and `lp2`**: Lists to store the Laplacian pyramids of `img1` and `img2`.
- **Initialization**: The top level of the Laplacian pyramid is the same as the top level of the Gaussian pyramid.
- **Loop**: This loop creates the Laplacian pyramid by taking the difference between each level of the Gaussian pyramid and the expanded version of the next level using `cv2.pyrUp`. This process enhances the edges in the images.

### Blend the Laplacian Pyramids
```python
    # Blend the Laplacian pyramids
    LS = []
    for l1, l2 in zip(lp1, lp2):
        rows, cols, dpt = l1.shape
        ls = np.hstack((l1[:, :cols // 2], l2[:, cols // 2:]))
        LS.append(ls)
```
- **`LS`**: List to store the blended Laplacian pyramid.
- **Loop**: This loop blends each level of the Laplacian pyramids by horizontally stacking the left half of `l1` (from `img1`) and the right half of `l2` (from `img2`).

### Reconstruct the Image
```python
    # Reconstruct the image
    ls_ = LS[0]
    for i in range(1, 6):
        ls_ = cv2.pyrUp(ls_)
        ls_ = cv2.add(ls_, LS[i])
    
    return ls_
```
- **`ls_`**: The reconstructed image starting from the smallest level of the blended Laplacian pyramid.
- **Loop**: This loop reconstructs the image by expanding each level (`cv2.pyrUp`) and adding it to the next level of the pyramid using `cv2.add`.

### Reading and Blending Images
```python
# Read the images
img1 = cv2.imread('apple.jpg')
img2 = cv2.imread('orange.jpg')

# Blend the images
blended_image = blend_images(img1, img2)
```
- **`img1` and `img2`**: Load the images from disk using `cv2.imread`.
- **`blended_image`**: Blend the images using the `blend_images` function.

### Displaying the Result
```python
# Display the result
cv2.imshow('Blended Image', blended_image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
- **`cv2.imshow`**: Displays the blended image in a window.
- **`cv2.waitKey(0)`**: Waits indefinitely for a key press. This keeps the window open until a key is pressed.
- **`cv2.destroyAllWindows`**: Closes all OpenCV windows.

### Summary
This code performs pyramid blending of two images, a technique used to blend two images seamlessly. The process involves creating Gaussian and Laplacian pyramids for each image, blending the pyramids, and then reconstructing the final blended image. The result is displayed in a window using OpenCV.

# OpenCV: Image Pyramids

## Goal

In this chapter:
- We will learn about Image Pyramids.
- We will use Image pyramids to create a new fruit, "Orapple".
- We will see these functions: `cv.pyrUp()`, `cv.pyrDown()`.

## Theory

Normally, we work with an image of constant size. But on some occasions, we need to work with the same images in different resolutions. For example, while searching for something in an image, like a face, we are not sure at what size the object will be present in the said image. In that case, we need to create a set of the same image with different resolutions and search for the object in all of them. These sets of images with different resolutions are called Image Pyramids (because when they are kept in a stack with the highest resolution image at the bottom and the lowest resolution image at the top, it looks like a pyramid).

There are two kinds of Image Pyramids:
1. Gaussian Pyramid
2. Laplacian Pyramid

A higher level (low resolution) in a Gaussian Pyramid is formed by removing consecutive rows and columns in the lower level (higher resolution) image. Then each pixel in the higher level is formed by the contribution from 5 pixels in the underlying level with gaussian weights. By doing so, an MxN image becomes M/2xN/2 image. So, the area reduces to one-fourth of the original area. It is called an Octave. The same pattern continues as we go upper in the pyramid (i.e., resolution decreases). Similarly, while expanding, the area becomes 4 times in each level. We can find Gaussian pyramids using `cv.pyrDown()` and `cv.pyrUp()` functions.

```python
img = cv.imread('messi5.jpg')
assert img is not None, "file could not be read, check with os.path.exists()"
lower_reso = cv.pyrDown(higher_reso)
```


Below are the 4 levels in an image pyramid.

![Image-1](https://docs.opencv.org/5.x/messipyr.jpg)

Now, you can go down the image pyramid with the `cv.pyrUp()` function.

```python
higher_reso2 = cv.pyrUp(lower_reso)
```

Remember, `higher_reso2` is not equal to `higher_reso` because once you decrease the resolution, you lose information. Below is an image 3 levels down the pyramid created from the smallest image in the previous case. Compare it with the original image.

![Image-2](https://docs.opencv.org/5.x/messiup.jpg)

Laplacian Pyramids are formed from the Gaussian Pyramids. There is no exclusive function for that. Laplacian pyramid images are like edge images only. Most of its elements are zeros. `They are used in image compression`. A level in a Laplacian Pyramid is formed by the difference between that level in the Gaussian Pyramid and the expanded version of its upper level in the Gaussian Pyramid.

![Image-3](https://docs.opencv.org/5.x/lap.jpg)

## Image Blending using Pyramids

One application of Pyramids is Image Blending. For example, in image stitching, you may need to stack two images together, but it may not look good due to discontinuities between the images. In that case, image blending with Pyramids gives you seamless blending without leaving much data in the images. One classical example of this is the blending of two fruits, Orange and Apple. See the result now itself to understand what I am saying:

![Image-4](https://docs.opencv.org/5.x/orapple.jpg)

Please check the first reference in additional resources; it has full diagrammatic details on image blending, Laplacian Pyramids, etc. Simply it is done as follows:

1. Load the two images of apple and orange.
2. Find the Gaussian Pyramids for apple and orange (in this particular example, the number of levels is 6).
3. From Gaussian Pyramids, find their Laplacian Pyramids.
4. Now join the left half of the apple and the right half of the orange in each level of the Laplacian Pyramids.
5. Finally, from this joint image pyramids, reconstruct the original image.

Below is the full code. (For simplicity, each step is done separately, which may take more memory. You can optimize it if you want to).

```python
import cv2 as cv
import numpy as np

A = cv.imread('apple.jpg')
B = cv.imread('orange.jpg')
assert A is not None, "file could not be read, check with os.path.exists()"
assert B is not None, "file could not be read, check with os.path.exists()"

# generate Gaussian pyramid for A
G = A.copy()
gpA = [G]
for i in range(6):
    G = cv.pyrDown(G)
    gpA.append(G)

# generate Gaussian pyramid for B
G = B.copy()
gpB = [G]
for i in range(6):
    G = cv.pyrDown(G)
    gpB.append(G)

# generate Laplacian Pyramid for A
lpA = [gpA[5]]
for i in range(5, 0, -1):
    GE = cv.pyrUp(gpA[i])
    L = cv.subtract(gpA[i-1], GE)
    lpA.append(L)

# generate Laplacian Pyramid for B
lpB = [gpB[5]]
for i in range(5, 0, -1):
    GE = cv.pyrUp(gpB[i])
    L = cv.subtract(gpB[i-1], GE)
    lpB.append(L)

# Now add left and right halves of images in each level
LS = []
for la, lb in zip(lpA, lpB):
    rows, cols, dpt = la.shape
    ls = np.hstack((la[:, 0:cols//2], lb[:, cols//2:]))
    LS.append(ls)

# now reconstruct
ls_ = LS[0]
for i in range(1, 6):
    ls_ = cv.pyrUp(ls_)
    ls_ = cv.add(ls_, LS[i])

# image with direct connecting each half
real = np.hstack((A[:, :cols//2], B[:, cols//2:]))

cv.imwrite('Pyramid_blending2.jpg', ls_)
cv.imwrite('Direct_blending.jpg', real)
```
<details>
    <summary>Explanation</summary>
## Explanation:

### 1. Importing Required Libraries
```python
import cv2 as cv
import numpy as np
```
- **`cv2`**: OpenCV library for image processing.
- **`numpy`**: Library for numerical operations, particularly with arrays (used here for image manipulation).

### 2. Reading the Images
```python
A = cv.imread('apple.jpg')
B = cv.imread('orange.jpg')
assert A is not None, "file could not be read, check with os.path.exists()"
assert B is not None, "file could not be read, check with os.path.exists()"
```
- **`cv.imread('apple.jpg')` and `cv.imread('orange.jpg')`**: Load the images `apple.jpg` and `orange.jpg` into variables `A` and `B`.
- **`assert A is not None`**: Ensures the image was successfully loaded; if not, an error message is shown.

### 3. Generating Gaussian Pyramid for Image A
```python
G = A.copy()
gpA = [G]
for i in range(6):
    G = cv.pyrDown(G)
    gpA.append(G)
```
- **`G = A.copy()`**: Create a copy of image `A` to start the pyramid.
- **`gpA = [G]`**: Initialize a list `gpA` to store the Gaussian pyramid of image `A`. It starts with the original image.
- **`cv.pyrDown(G)`**: Applies Gaussian blurring followed by downsampling (reducing the image size by half). This is done iteratively to create a pyramid.
- **`gpA.append(G)`**: Add each downsampled image to the pyramid list `gpA`.

### 4. Generating Gaussian Pyramid for Image B
```python
G = B.copy()
gpB = [G]
for i in range(6):
    G = cv.pyrDown(G)
    gpB.append(G)
```
- This process is identical to the one used for `A`, but it generates the Gaussian pyramid for image `B`, storing it in `gpB`.

### 5. Generating Laplacian Pyramid for Image A
```python
lpA = [gpA[5]]
for i in range(5, 0, -1):
    GE = cv.pyrUp(gpA[i])
    L = cv.subtract(gpA[i-1], GE)
    lpA.append(L)
```
- **`lpA = [gpA[5]]`**: Initialize the Laplacian pyramid `lpA` with the smallest image in the Gaussian pyramid (the last one in `gpA`).
- **`cv.pyrUp(gpA[i])`**: Upsample the image (increases the size by 2x) to match the size of the next level up in the pyramid.
- **`cv.subtract(gpA[i-1], GE)`**: Subtract the upsampled image from the corresponding level in the Gaussian pyramid to get the Laplacian image.
- **`lpA.append(L)`**: Add the Laplacian image to the `lpA` list.

### 6. Generating Laplacian Pyramid for Image B
```python
lpB = [gpB[5]]
for i in range(5, 0, -1):
    GE = cv.pyrUp(gpB[i])
    L = cv.subtract(gpB[i-1], GE)
    lpB.append(L)
```
- This step is identical to the one used for `A`, but it generates the Laplacian pyramid for image `B`, storing it in `lpB`.

### 7. Combining the Two Images at Each Level of the Pyramid
```python
LS = []
for la, lb in zip(lpA, lpB):
    rows, cols, dpt = la.shape
    ls = np.hstack((la[:, 0:cols//2], lb[:, cols//2:]))
    LS.append(ls)
```
- **`zip(lpA, lpB)`**: Pairs the corresponding levels of the Laplacian pyramids of `A` and `B`.More about code snippet, [for la, lb in zip(lpA, lpB)](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/2.4.md)
- **`np.hstack((la[:, 0:cols//2], lb[:, cols//2:]))`**: Horizontally stacks the left half of `la` (Laplacian of `A`) with the right half of `lb` (Laplacian of `B`).
- **`LS.append(ls)`**: Adds the combined image at each level to the `LS` list.

### 8. Reconstructing the Final Blended Image
```python
ls_ = LS[0]
for i in range(1, 6):
    ls_ = cv.pyrUp(ls_)
    ls_ = cv.add(ls_, LS[i])
```
- **`ls_ = LS[0]`**: Start with the smallest combined image.
- **`cv.pyrUp(ls_)`**: Upsamples the image to the next level.
- **`cv.add(ls_, LS[i])`**: Add the current upsampled image to the corresponding level in `LS` to reconstruct the final blended image.

### 9. Direct Blending of the Two Images (Without Pyramids)
```python
real = np.hstack((A[:, :cols//2], B[:, cols//2:]))
```
- **`np.hstack((A[:, :cols//2], B[:, cols//2:]))`**: Directly stack the left half of image `A` with the right half of image `B`.

### 10. Saving the Blended Images
```python
cv.imwrite('Pyramid_blending2.jpg', ls_)
cv.imwrite('Direct_blending.jpg', real)
```
- **`cv.imwrite('Pyramid_blending2.jpg', ls_)`**: Save the pyramid-blended image as `Pyramid_blending2.jpg`.
- **`cv.imwrite('Direct_blending.jpg', real)`**: Save the directly blended image as `Direct_blending.jpg`.
</details>
<details>
<summary>For resizing the images</summary>
- If you face any errors then you can do the resizing , mostly it should work.

```python
import cv2 as cv
import numpy as np

# Load images
A = cv.imread("images/messi1.png")
assert A is not None, "File could not be read, check with os.path.exists()"
B = cv.imread("images/ronaldo.png")
assert B is not None, "File could not be read, check with os.path.exists()"

# Generate Gaussian pyramid for A
G = A.copy()
gpA = [G]
for i in range(6):
    G = cv.pyrDown(G)
    gpA.append(G)

# Generate Gaussian pyramid for B
G = B.copy()
gpB = [G]
for i in range(6):
    G = cv.pyrDown(G)
    gpB.append(G)

# Generate Laplacian Pyramid for A
lpA = [gpA[5]]
for i in range(5, 0, -1):
    GE = cv.pyrUp(gpA[i])
    GE = cv.resize(GE, (gpA[i-1].shape[1], gpA[i-1].shape[0]))  # Resize to match shape
    L = cv.subtract(gpA[i-1], GE)
    lpA.append(L)

# Generate Laplacian Pyramid for B
lpB = [gpB[5]]
for i in range(5, 0, -1):
    GE = cv.pyrUp(gpB[i])
    GE = cv.resize(GE, (gpB[i-1].shape[1], gpB[i-1].shape[0]))  # Resize to match shape
    L = cv.subtract(gpB[i-1], GE)
    lpB.append(L)

# Now add left and right halves of the images in each level
LS = []
for la, lb in zip(lpA, lpB):
    la = cv.resize(la, (min(la.shape[1], lb.shape[1]), min(la.shape[0], lb.shape[0])))  # Resize to the smallest shape
    lb = cv.resize(lb, (min(la.shape[1], lb.shape[1]), min(la.shape[0], lb.shape[0])))
    rows, cols, dpt = la.shape
    ls = np.hstack((la[:, :cols//2], lb[:, cols//2:]))
    LS.append(ls)

# Reconstruct the image
ls_ = LS[0]
for i in range(1, 6):
    ls_ = cv.pyrUp(ls_)
    ls_ = cv.resize(ls_, (LS[i].shape[1], LS[i].shape[0]))  # Resize to match the shape
    ls_ = cv.add(ls_, LS[i])

# Direct blending without pyramids
cols = A.shape[1]
real = np.hstack((A[:, :cols//2], B[:, cols//2:]))

# Display results
cv.imshow("Pyramid Blend", ls_)
cv.imshow("Direct Blend", real)

if cv.waitKey(0) & 0xFF == 27:
    cv.destroyAllWindows()
```

### Key Fixes:
1. **Resizing Arrays Before Concatenation**:
   - Before horizontally stacking (`np.hstack`) the two arrays `la` and `lb`, they are resized to have matching dimensions. This ensures that they can be concatenated without any dimension mismatch errors.

2. **General Resize Handling**:
   - Throughout the pyramid construction and reconstruction, ensure that any image being processed is resized to match the target dimensions to avoid similar errors.

This should solve the `ValueError` related to mismatched dimensions during the concatenation process.
</details>
## Additional Resources

1. [Image Blending](http://pages.cs.wisc.edu/~csverma/CS766_09/ImageMosaic/imagemosaic.html)

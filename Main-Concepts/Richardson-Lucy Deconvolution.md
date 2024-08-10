# Image Deconvolution

In this example, we deconvolve an image using the Richardson-Lucy deconvolution algorithm ([1], [2]).

The algorithm is based on a PSF (Point Spread Function), where PSF is described as the impulse response of the optical system. The blurred image is sharpened through a number of iterations, which need to be hand-tuned.

### References

[1] William Hadley Richardson, “Bayesian-Based Iterative Method of Image Restoration”, J. Opt. Soc. Am. A 27, 1593-1607 (1972), DOI: [10.1364/JOSA.62.000055](https://doi.org/10.1364/JOSA.62.000055)

[2] [https://en.wikipedia.org/wiki/Richardson%E2%80%93Lucy_deconvolution](https://en.wikipedia.org/wiki/Richardson%E2%80%93Lucy_deconvolution)

## Original Data, Noisy Data, and Restoration using Richardson-Lucy

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import convolve2d as conv2
from skimage import color, data, restoration

rng = np.random.default_rng()

astro = color.rgb2gray(data.astronaut())

psf = np.ones((5, 5)) / 25
astro = conv2(astro, psf, 'same')
# Add Noise to Image
astro_noisy = astro.copy()
astro_noisy += (rng.poisson(lam=25, size=astro.shape) - 10) / 255.0

# Restore Image using Richardson-Lucy algorithm
deconvolved_RL = restoration.richardson_lucy(astro_noisy, psf, num_iter=30)

fig, ax = plt.subplots(nrows=1, ncols=3, figsize=(8, 5))
plt.gray()

for a in (ax[0], ax[1], ax[2]):
    a.axis('off')

ax[0].imshow(astro)
ax[0].set_title('Original Data')

ax[1].imshow(astro_noisy)
ax[1].set_title('Noisy data')

ax[2].imshow(deconvolved_RL, vmin=astro_noisy.min(), vmax=astro_noisy.max())
ax[2].set_title('Restoration using\nRichardson-Lucy')

fig.subplots_adjust(wspace=0.02, hspace=0.2, top=0.9, bottom=0.05, left=0, right=1)
plt.show()
```
## Explanation

### `import numpy as np`
- **Purpose**: This imports the NumPy library and assigns it the alias `np`.
- **Example**: Instead of writing `numpy.array([1, 2, 3])`, you can write `np.array([1, 2, 3])`.
- **Use Case**: NumPy is essential for numerical computations, especially when working with arrays and matrices.

### `import matplotlib.pyplot as plt`
- **Purpose**: Imports the `pyplot` module from Matplotlib and assigns it the alias `plt`.
- **Example**: To create a plot, you would use `plt.plot([1, 2, 3])`.
- **Use Case**: `pyplot` is used for creating static, animated, and interactive visualizations in Python.

### `from scipy.signal import convolve2d as conv2`
- **Purpose**: Imports the `convolve2d` function from the `scipy.signal` module and assigns it the alias `conv2`.
- **Example**: You can call `conv2(array1, array2)` instead of `convolve2d(array1, array2)`.
- **Use Case**: Used for performing 2D convolution operations, which is common in image processing.

### `from skimage import color, data, restoration`
- **Purpose**: Imports specific modules from the `skimage` library:
  - `color` for color space conversions.
  - `data` for accessing example datasets.
  - `restoration` for image restoration algorithms.
- **Example**: You can convert an RGB image to grayscale using `color.rgb2gray(image)`.
- **Use Case**: `skimage` is a library focused on image processing tasks.

### `rng = np.random.default_rng()`
- **Purpose**: Creates a random number generator (`rng`) using NumPy's new `default_rng()` method.
- **Example**: You can generate random numbers with `rng.random()`.
- **Use Case**: Random number generation is useful for adding noise to data, simulating random processes, etc.

### `astro = color.rgb2gray(data.astronaut())`
- **Purpose**: Loads the astronaut image from `skimage`’s example datasets, then converts it from RGB to grayscale.
- **Example**: `astro` will now contain a 2D array representing the grayscale image.
- **Use Case**: Grayscale conversion is often the first step in image processing, reducing computational complexity by removing color information.

### `psf = np.ones((5, 5)) / 25`
- **Purpose**: Creates a 5x5 point spread function (PSF) matrix, where all elements are `1/25`.
- **Explanation**:
  - `np.ones((5, 5))` creates a 5x5 matrix filled with ones.
  - Dividing by 25 normalizes the sum of the matrix to 1.
- **Use Case**: PSFs are used in convolution to simulate the blurring effect of a camera or imaging system.

### `astro = conv2(astro, psf, 'same')`
- **Purpose**: Convolves the grayscale image `astro` with the PSF using 2D convolution.
- **Explanation**:
  - The `conv2` function performs convolution.
  - The `'same'` argument ensures that the output image has the same size as the input.
- **Use Case**: Simulates the blurring of an image by an optical system, which is common in image processing tasks like deblurring.

### `astro_noisy = astro.copy()`
- **Purpose**: Creates a copy of the blurred image `astro` and assigns it to `astro_noisy`.
- **Example**: This ensures that any subsequent changes to `astro_noisy` do not affect the original `astro` image.
- **Use Case**: Copying is useful when you need to manipulate a version of an array without altering the original data.

### `astro_noisy += (rng.poisson(lam=25, size=astro.shape) - 10) / 255.0`
- **Purpose**: Adds Poisson-distributed noise to the `astro_noisy` image.
- **Explanation**:
  - `rng.poisson(lam=25, size=astro.shape)` generates Poisson noise with a mean (`lam`) of 25.
  - Subtracting 10 centers the noise around zero.
  - Dividing by 255.0 scales the noise to be appropriate for image data.
- **Use Case**: Adding noise simulates real-world conditions where images are often corrupted by various types of noise.

### `deconvolved_RL = restoration.richardson_lucy(astro_noisy, psf, num_iter=30)`
- **Purpose**: Applies the Richardson-Lucy deconvolution algorithm to the noisy image `astro_noisy`.
- **Explanation**:
  - `restoration.richardson_lucy` is a method for image deblurring, using the PSF to reverse the blurring effect.
  - `num_iter=30` specifies the number of iterations for the algorithm to run.
- **Use Case**: Deconvolution is used in image restoration to recover a sharp image from a blurred one.

### `fig, ax = plt.subplots(nrows=1, ncols=3, figsize=(8, 5))`
- **Purpose**: Creates a figure and a set of subplots (axes) arranged in a 1x3 grid, with the figure size set to 8x5 inches.
- **Explanation**:
  - `nrows=1, ncols=3` creates a single row of three subplots.
  - `figsize=(8, 5)` sets the size of the figure.
- **Use Case**: Useful for comparing multiple images side by side, as is often needed in image processing demonstrations.

### `plt.gray()`
- **Purpose**: Sets the colormap to grayscale for all subsequent plots.
- **Use Case**: Ensures that images are displayed in grayscale, which is appropriate for the `astro`, `astro_noisy`, and `deconvolved_RL` images.

### `for a in (ax[0], ax[1], ax[2]): a.axis('off')`
- **Purpose**: Iterates over each axis `a` in the `ax` array and turns off the axis lines and labels.
- **Use Case**: Removes distractions from the image, focusing the viewer's attention solely on the content.

### `ax[0].imshow(astro)`
- **Purpose**: Displays the original blurred image `astro` on the first subplot.
- **Use Case**: Provides a reference for comparison with the noisy and restored images.

### `ax[0].set_title('Original Data')`
- **Purpose**: Sets the title of the first subplot to "Original Data".
- **Use Case**: Labels the subplot to indicate that it shows the original blurred image.

### `ax[1].imshow(astro_noisy)`
- **Purpose**: Displays the noisy image `astro_noisy` on the second subplot.
- **Use Case**: Allows comparison between the original blurred image and the noisy image.

### `ax[1].set_title('Noisy data')`
- **Purpose**: Sets the title of the second subplot to "Noisy data".
- **Use Case**: Labels the subplot to indicate that it shows the noisy version of the image.

### `ax[2].imshow(deconvolved_RL, vmin=astro_noisy.min(), vmax=astro_noisy.max())`
- **Purpose**: Displays the restored image `deconvolved_RL` on the third subplot.
- **Explanation**:
  - `vmin=astro_noisy.min(), vmax=astro_noisy.max()` ensures the color scaling matches that of the noisy image.
- **Use Case**: Visualizes the effectiveness of the deblurring algorithm by comparing it with the original noisy image.

### `ax[2].set_title('Restoration using\nRichardson-Lucy')`
- **Purpose**: Sets the title of the third subplot to "Restoration using Richardson-Lucy".
- **Use Case**: Labels the subplot to indicate that it shows the result of the Richardson-Lucy deconvolution.

### `fig.subplots_adjust(wspace=0.02, hspace=0.2, top=0.9, bottom=0.05, left=0, right=1)`
- **Purpose**: Adjusts the spacing and layout of the subplots within the figure.
- **Explanation**:
  - `wspace` and `hspace` control the width and height space between subplots.
  - `top`, `bottom`, `left`, `right` control the margins around the subplots.
- **Use Case**: Fine-tunes the layout to ensure that the subplots are visually appealing and well-organized.

### `plt.show()`
- **Purpose**: Displays the figure with all the subplots.
- **Use Case**: Finalizes and presents the visualization, making it ready for interpretation or presentation.

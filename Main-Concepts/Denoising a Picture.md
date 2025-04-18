# Denoising a Picture

In this example, we denoise a noisy version of a picture using the total variation, bilateral, and wavelet denoising filters.

Total variation and bilateral algorithms typically produce "posterized" images with flat domains separated by sharp edges. It is possible to change the degree of posterization by controlling the tradeoff between denoising and faithfulness to the original image.

## Total Variation Filter

The result of this filter is an image that has a minimal total variation norm while being as close to the initial image as possible. The total variation is the L1 norm of the gradient of the image.

## Bilateral Filter

A bilateral filter is an edge-preserving and noise-reducing filter. It averages pixels based on their spatial closeness and radiometric similarity.

## Wavelet Denoising Filter

A wavelet denoising filter relies on the wavelet representation of the image. The noise is represented by small values in the wavelet domain, which are set to 0.

In color images, wavelet denoising is typically done in the [YCbCr color space](https://en.wikipedia.org/wiki/YCbCr), as denoising in separate color channels may lead to more apparent noise.

![Image-2](https://scikit-image.org/docs/stable/_images/sphx_glr_plot_denoise_001.png)

### Out:

> Estimated Gaussian noise standard deviation = 0.14973509196387422
> Clipping input data to the valid range for imshow with RGB data ([0..1] for floats or [0..255] for integers). Got range [-0.016322852617172822..0.8737733532678398].

```python
import matplotlib.pyplot as plt

from skimage.restoration import (
    denoise_tv_chambolle,
    denoise_bilateral,
    denoise_wavelet,
    estimate_sigma,
)
from skimage import data, img_as_float
from skimage.util import random_noise

original = img_as_float(data.chelsea()[100:250, 50:300])

sigma = 0.155
noisy = random_noise(original, var=sigma**2)

fig, ax = plt.subplots(nrows=2, ncols=4, figsize=(8, 5), sharex=True, sharey=True)

plt.gray()

# Estimate the average noise standard deviation across color channels.
sigma_est = estimate_sigma(noisy, channel_axis=-1, average_sigmas=True)
# Due to clipping in random_noise, the estimate will be a bit smaller than the
# specified sigma.
print(f'Estimated Gaussian noise standard deviation = {sigma_est}')

ax[0, 0].imshow(noisy)
ax[0, 0].axis('off')
ax[0, 0].set_title('Noisy')
ax[0, 1].imshow(denoise_tv_chambolle(noisy, weight=0.1, channel_axis=-1))
ax[0, 1].axis('off')
ax[0, 1].set_title('TV')
ax[0, 2].imshow(
    denoise_bilateral(noisy, sigma_color=0.05, sigma_spatial=15, channel_axis=-1)
)
ax[0, 2].axis('off')
ax[0, 2].set_title('Bilateral')
ax[0, 3].imshow(denoise_wavelet(noisy, channel_axis=-1, rescale_sigma=True))
ax[0, 3].axis('off')
ax[0, 3].set_title('Wavelet denoising')

ax[1, 1].imshow(denoise_tv_chambolle(noisy, weight=0.2, channel_axis=-1))
ax[1, 1].axis('off')
ax[1, 1].set_title('(more) TV')
ax[1, 2].imshow(
    denoise_bilateral(noisy, sigma_color=0.1, sigma_spatial=15, channel_axis=-1)
)
ax[1, 2].axis('off')
ax[1, 2].set_title('(more) Bilateral')
ax[1, 3].imshow(
    denoise_wavelet(noisy, channel_axis=-1, convert2ycbcr=True, rescale_sigma=True)
)
ax[1, 3].axis('off')
ax[1, 3].set_title('Wavelet denoising\nin YCbCr colorspace')
ax[1, 0].imshow(original)
ax[1, 0].axis('off')
ax[1, 0].set_title('Original')

fig.tight_layout()

plt.show()
```

## Explanation

### `import matplotlib.pyplot as plt`

- **Purpose**: Imports the `pyplot` module from the `matplotlib` library and assigns it the alias `plt`.
- **Example**: `plt.plot([1, 2, 3])` creates a simple line plot.
- **Use Case**: `pyplot` is used for creating various types of plots and visualizations in Python, such as line plots, histograms, and scatter plots.

### `from skimage.restoration import (denoise_tv_chambolle, denoise_bilateral, denoise_wavelet, estimate_sigma)`

- **Purpose**: Imports specific denoising functions and a noise estimation function from the `skimage.restoration` module.
  - `denoise_tv_chambolle`: Implements Total Variation (TV) denoising.
  - `denoise_bilateral`: Implements Bilateral filtering for denoising.
  - `denoise_wavelet`: Implements Wavelet-based denoising.
  - `estimate_sigma`: Estimates the noise standard deviation (sigma) in an image.
- **Use Case**: These functions are used to reduce noise in images using different algorithms, each with its strengths and applications.

### `from skimage import data, img_as_float`

- **Purpose**: Imports the `data` module for accessing example datasets and the `img_as_float` function for converting images to floating-point format.
- **Example**: `img_as_float(image)` converts an image to a float representation with pixel values between 0 and 1.
- **Use Case**: Converting images to float is often necessary for image processing tasks that involve arithmetic operations or denoising.

### `from skimage.util import random_noise`

- **Purpose**: Imports the `random_noise` function, which adds noise to an image.
- **Example**: `random_noise(image, var=0.01)` adds Gaussian noise with a variance of 0.01.
- **Use Case**: Useful for simulating noisy images to test the effectiveness of denoising algorithms.

### `original = img_as_float(data.chelsea()[100:250, 50:300])`

- **Purpose**: Loads the "chelsea" cat image from `skimage`'s example datasets, crops a specific region, and converts it to a float representation.
- **Explanation**:
  - `data.chelsea()` loads the image.
  - `[100:250, 50:300]` crops the image to focus on a specific part.
  - `img_as_float()` converts the image to a float type.
- **Use Case**: Working with a cropped region of the image allows focusing on a smaller area for more detailed analysis.

### `sigma = 0.155`

- **Purpose**: Defines the noise level (standard deviation) to be added to the image.
- **Example**: `sigma` represents the standard deviation of the Gaussian noise that will be added to the image.
- **Use Case**: Setting a specific noise level helps in testing the robustness of different denoising methods.

### `noisy = random_noise(original, var=sigma**2)`

- **Purpose**: Adds Gaussian noise with variance `sigma^2` to the `original` image and stores the result in `noisy`.
- **Explanation**:
  - `random_noise(original, var=sigma**2)` adds noise where `var=sigma**2` sets the variance of the noise.
- **Use Case**: Simulating noisy data is a common practice when testing and evaluating image processing algorithms, especially denoising techniques.

### `fig, ax = plt.subplots(nrows=2, ncols=4, figsize=(8, 5), sharex=True, sharey=True)`

- **Purpose**: Creates a figure with a grid of subplots (2 rows and 4 columns) to display images side by side.
- **Explanation**:
  - `nrows=2, ncols=4` specifies the layout of subplots.
  - `figsize=(8, 5)` sets the overall size of the figure.
  - `sharex=True, sharey=True` ensures that all subplots share the same x and y axes.
- **Use Case**: This layout is useful for comparing multiple images, such as the original, noisy, and denoised versions, in a compact and organized manner.

### `plt.gray()`

- **Purpose**: Sets the colormap to grayscale for all subsequent image plots.
- **Use Case**: Ensures that images are displayed in grayscale, which is often desirable in image processing tasks to focus on intensity variations rather than color.

### `sigma_est = estimate_sigma(noisy, channel_axis=-1, average_sigmas=True)`

- **Purpose**: Estimates the noise standard deviation in the noisy image using the `estimate_sigma` function.
- **Explanation**:
  - `channel_axis=-1` indicates that the color channels are in the last dimension of the image array.
  - `average_sigmas=True` calculates an average sigma value across all color channels.
- **Use Case**: Estimating the noise level in an image is a crucial step before applying denoising algorithms, as it helps in fine-tuning the denoising parameters.

### `print(f'Estimated Gaussian noise standard deviation = {sigma_est}')`

- **Purpose**: Prints the estimated noise standard deviation to the console.
- **Example**: This line helps verify how closely the estimated noise level matches the actual noise level added (`sigma`).
- **Use Case**: Useful for debugging and understanding the performance of the noise estimation function.

### `ax[0, 0].imshow(noisy)`

- **Purpose**: Displays the noisy image in the first subplot of the grid (top-left).
- **Use Case**: Visualizes the noisy version of the image, providing a baseline for comparison with denoised images.

### `ax[0, 0].axis('off')`

- **Purpose**: Turns off the axis lines and labels for the first subplot.
- **Use Case**: Removes unnecessary details from the plot, allowing the viewer to focus solely on the image content.

### `ax[0, 0].set_title('Noisy')`

- **Purpose**: Sets the title of the first subplot to "Noisy".
- **Use Case**: Labels the subplot to indicate that it shows the noisy version of the image.

### `ax[0, 1].imshow(denoise_tv_chambolle(noisy, weight=0.1, channel_axis=-1))`

- **Purpose**: Applies Total Variation (TV) denoising with a weight of 0.1 to the noisy image and displays the result in the second subplot.
- **Explanation**:
  - `weight=0.1` controls the strength of the denoising. Lower values preserve more detail but remove less noise.
- **Use Case**: TV denoising is particularly effective for reducing noise while preserving edges, making it suitable for images with sharp transitions.

### `ax[0, 1].axis('off')`

- **Purpose**: Turns off the axis lines and labels for the second subplot.
- **Use Case**: As before, this declutters the visualization.

### `ax[0, 1].set_title('TV')`

- **Purpose**: Sets the title of the second subplot to "TV".
- **Use Case**: Labels the subplot to indicate that it shows the result of TV denoising.

### `ax[0, 2].imshow(denoise_bilateral(noisy, sigma_color=0.05, sigma_spatial=15, channel_axis=-1))`

- **Purpose**: Applies Bilateral filtering with specified parameters to the noisy image and displays the result in the third subplot.
- **Explanation**:
  - `sigma_color=0.05` controls the filter's sensitivity to intensity differences.
  - `sigma_spatial=15` controls the filter's sensitivity to spatial differences.
- **Use Case**: Bilateral filtering is useful for denoising while preserving edges, making it suitable for images where edge information is critical.

### `ax[0, 2].axis('off')`

- **Purpose**: Turns off the axis lines and labels for the third subplot.
- **Use Case**: Continues the pattern of decluttering the subplot.

### `ax[0, 2].set_title('Bilateral')`

- **Purpose**: Sets the title of the third subplot to "Bilateral".
- **Use Case**: Labels the subplot to indicate that it shows the result of Bilateral filtering.

### `ax[0, 3].imshow(denoise_wavelet(noisy, channel_axis=-1, rescale_sigma=True))`

- **Purpose**: Applies Wavelet denoising to the noisy image and displays the result in the fourth subplot.
- **Explanation**:
  - `rescale_sigma=True` rescales the estimated noise standard deviation for the denoising process.
- **Use Case**: Wavelet denoising is effective for multi-scale noise reduction, making it suitable for images with complex noise patterns.

### `ax[0, 3].axis('off')`

- **Purpose**: Turns off the axis lines and labels for the fourth subplot.
- **Use Case**: Maintains consistency across all subplots by removing unnecessary details.

### `ax[0, 3].set_title('Wavelet denoising')`

- **Purpose**: Sets the title of the fourth subplot to "Wavelet denoising".
- **Use Case**: Labels the subplot to indicate that it shows the result of Wavelet denoising.

### `ax[1, 1].imshow(denoise_tv_chambolle(noisy, weight=0.2, channel_axis=-1))`

- **Purpose**: Applies TV denoising with a higher weight of 0.2 to the noisy image and displays the result in the fifth subplot (first on the second row).
- **Explanation**:
  - A higher `weight` results in stronger denoising but may also blur some details.
- **Use Case**: This subplot allows comparison between different strengths of TV denoising.

### `ax[1, 1].axis('off')`

- **Purpose**: Turns off the axis lines and labels for the fifth subplot.
- **Use Case**: Keeps the visualization clean.

### `ax[1, 1].set_title('(more) TV')`

- **Purpose**: Sets the title of the fifth subplot to "(more) TV".
- **Use Case**: Indicates that this subplot shows a stronger version of TV denoising.

### `ax[1, 2].imshow(denoise_bilateral(noisy, sigma_color=0.1, sigma_spatial=15, channel_axis=-1))`

- **Purpose**: Applies Bilateral filtering with a higher `sigma_color` of 0.1 to the noisy image and displays the result in the sixth subplot.
- **Explanation**:
  - A higher `sigma_color` makes the filter more tolerant to intensity variations, potentially preserving more details.
- **Use Case**: This subplot allows comparison between different strengths of Bilateral filtering.

### `ax[1, 2].axis('off')`

- **Purpose**: Turns off the axis lines and labels for the sixth subplot.
- **Use Case**: Keeps the visualization uniform and clean.

### `ax[1, 2].set_title('(more) Bilateral')`

- **Purpose**: Sets the title of the sixth subplot to "(more) Bilateral".
- **Use Case**: Indicates that this subplot shows a stronger version of Bilateral filtering.

### `ax[1, 3].imshow(denoise_wavelet(noisy, channel_axis=-1, convert2ycbcr=True, rescale_sigma=True))`

- **Purpose**: Applies Wavelet denoising in the YCbCr color space and displays the result in the seventh subplot.
- **Explanation**:
  - `convert2ycbcr=True` converts the image to the YCbCr color space before denoising, which may provide better results for certain images.
- **Use Case**: This subplot demonstrates the effect of applying Wavelet denoising in a different color space.

### `ax[1, 3].axis('off')`

- **Purpose**: Turns off the axis lines and labels for the seventh subplot.
- **Use Case**: Maintains the consistency of the visualization.

### `ax[1, 3].set_title('Wavelet denoising\nin YCbCr colorspace')`

- **Purpose**: Sets the title of the seventh subplot to "Wavelet denoising in YCbCr colorspace".
- **Use Case**: Labels the subplot to indicate the use of a different color space for denoising.

### `ax[1, 0].imshow(original)`

- **Purpose**: Displays the original (noise-free) image in the eighth subplot (first on the second row).
- **Use Case**: Provides a reference for comparison with the noisy and denoised images.

### `ax[1, 0].axis('off')`

- **Purpose**: Turns off the axis lines and labels for the eighth subplot.
- **Use Case**: Keeps the visualization clean and focused on the image.

### `ax[1, 0].set_title('Original')`

- **Purpose**: Sets the title of the eighth subplot to "Original".
- **Use Case**: Labels the subplot to indicate that it shows the original, noise-free image.

### `fig.tight_layout()`

- **Purpose**: Adjusts the layout of the figure to minimize overlapping elements and ensure everything fits neatly within the figure.
- **Use Case**: Improves the visual appearance of the figure, making it easier to compare subplots without clutter.

### `plt.show()`

- **Purpose**: Displays the entire figure with all the subplots in the current output device (e.g., a window or a Jupyter notebook).
- **Use Case**: Renders the final visualization, allowing you to see the results of the different denoising techniques applied to the noisy image.

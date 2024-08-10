# Smoothing of a 1D Signal


This method is based on the convolution of a scaled window with the signal. The signal is prepared by introducing reflected window-length copies of the signal at both ends so that boundary effects are minimized in the beginning and end parts of the output signal.

## Code

```python
import numpy

def smooth(x, window_len=11, window='hanning'):
    """
    Smooth the data using a window with the requested size.
    
    This method is based on the convolution of a scaled window with the signal.
    The signal is prepared by introducing reflected copies of the signal 
    (with the window size) at both ends so that transient parts are minimized
    in the beginning and end parts of the output signal.
    
    Parameters:
        x: The input signal 
        window_len: The dimension of the smoothing window; should be an odd integer
        window: The type of window from 'flat', 'hanning', 'hamming', 'bartlett', 'blackman'
            A flat window will produce a moving average smoothing.

    Returns:
        The smoothed signal.
        
    Example:

    t = linspace(-2, 2, 0.1)
    x = sin(t) + randn(len(t)) * 0.1
    y = smooth(x)
    
    See also: 
    
    numpy.hanning, numpy.hamming, numpy.bartlett, numpy.blackman, numpy.convolve
    scipy.signal.lfilter
 
    TODO: The window parameter could be the window itself if an array instead of a string.
    NOTE: length(output) != length(input). To correct this: return y[(window_len/2-1):-(window_len/2)] instead of just y.
    """

    if x.ndim != 1:
        raise ValueError("smooth only accepts 1D arrays.")

    if x.size < window_len:
        raise ValueError("Input vector needs to be bigger than window size.")

    if window_len < 3:
        return x

    if window not in ['flat', 'hanning', 'hamming', 'bartlett', 'blackman']:
        raise ValueError("Window must be one of 'flat', 'hanning', 'hamming', 'bartlett', 'blackman'.")

    s = numpy.r_[x[window_len-1:0:-1], x, x[-2:-window_len-1:-1]]
    
    if window == 'flat':  # Moving average
        w = numpy.ones(window_len, 'd')
    else:
        w = eval('numpy.' + window + '(window_len)')

    y = numpy.convolve(w/w.sum(), s, mode='valid')
    return y
```

```python
from numpy import *
from pylab import *

def smooth_demo():
    t = linspace(-4, 4, 100)
    x = sin(t)
    xn = x + randn(len(t)) * 0.1
    y = smooth(x)

    ws = 31

    subplot(211)
    plot(ones(ws))

    windows = ['flat', 'hanning', 'hamming', 'bartlett', 'blackman']

    hold(True)
    for w in windows[1:]:
        eval('plot(' + w + '(ws))')

    axis([0, 30, 0, 1.1])

    legend(windows)
    title("The smoothing windows")
    
    subplot(212)
    plot(x)
    plot(xn)
    for w in windows:
        plot(smooth(xn, 10, w))
    l = ['original signal', 'signal with noise']
    l.extend(windows)

    legend(l)
    title("Smoothing a noisy signal")
    show()

if __name__ == '__main__':
    smooth_demo()
```
## Figure :

![Image-1](https://scipy-cookbook.readthedocs.io/_static/items/attachments/SignalSmooth/smoothsignal.jpg)

# Smoothing of a 2D Signal

Convolving a noisy image with a Gaussian kernel (or any bell-shaped curve) blurs the noise out and leaves the low-frequency details of the image standing out.

## Functions Used

```python
def gauss_kern(size, sizey=None):
    """ Returns a normalized 2D Gaussian kernel array for convolutions """
    size = int(size)
    if not sizey:
        sizey = size
    else:
        sizey = int(sizey)
    x, y = mgrid[-size:size+1, -sizey:sizey+1]
    g = exp(-(x**2 / float(size) + y**2 / float(sizey)))
    return g / g.sum()

def blur_image(im, n, ny=None):
    """
    Blurs the image by convolving with a Gaussian kernel of typical size n.
    The optional keyword argument ny allows for a different size in the y-direction.
    """
    g = gauss_kern(n, sizey=ny)
    improc = signal.convolve(im, g, mode='valid')
    return improc
```

## Example

```python
from scipy import *

X, Y = mgrid[-70:70, -70:70]
Z = cos((X**2 + Y**2) / 200.) + random.normal(size=X.shape)
```
![Image-2](https://scipy-cookbook.readthedocs.io/_static/items/attachments/SignalSmooth/noisy.png)

```py
blur_image(Z, 3)
```
![Image-3](https://scipy-cookbook.readthedocs.io/_static/items/attachments/SignalSmooth/convolved.png)

The attachment `cookb_signalsmooth.py` contains a version of this script with some stylistic cleanup.


---

## Explanation

### `import numpy`
- **Purpose**: This line imports the NumPy library, which provides support for large, multi-dimensional arrays and matrices, along with a collection of mathematical functions to operate on these arrays.
- **Example**: 
  - If you want to create an array `[1, 2, 3]`, you can do so using `numpy.array([1, 2, 3])`.
  - **Use Case**: Used in data processing and manipulation in scientific computing.

### `def smooth(x, window_len=11, window='hanning'):`
- **Purpose**: Defines the `smooth` function that applies a smoothing algorithm to a 1D signal.
- **Parameters**:
  - `x`: Input 1D array (signal).
  - `window_len`: Size of the smoothing window; defaults to 11.
  - `window`: Type of window function used; defaults to 'hanning'.
- **Example**: 
  - To smooth a noisy signal `x`, you could call `smooth(x, window_len=15, window='hamming')`.
  - **Use Case**: Smoothing time-series data or any 1D signal to remove noise.

### `"""smooth the data using a window with requested size...`
- **Purpose**: This is a docstring that explains what the `smooth` function does.
- **Explanation**:
  - **Convolution**: The function uses convolution with a window function to smooth the signal.
  - **Boundary Handling**: The function prepares the signal by reflecting its edges to minimize boundary effects.
  - **Parameters and Outputs**:
    - `x`: The input signal.
    - `window_len`: Dimension of the smoothing window, which should be an odd integer.
    - `window`: Type of window function (`flat`, `hanning`, `hamming`, `bartlett`, `blackman`).
    - **Output**: The smoothed signal.

### `if x.ndim != 1: raise ValueError, "smooth only accepts 1 dimension arrays."`
- **Purpose**: This checks if the input `x` is a 1D array. If not, it raises a `ValueError`.
- **Example**: 
  - If `x` is a 2D array like `numpy.array([[1, 2], [3, 4]])`, this will trigger the error.
  - **Use Case**: Ensures that only 1D signals are processed.

### `if x.size < window_len: raise ValueError, "Input vector needs to be bigger than window size."`
- **Purpose**: Ensures that the length of the input signal `x` is greater than the window size.
- **Example**: 
  - If `x` has 10 elements but `window_len` is 15, this will trigger the error.
  - **Use Case**: Avoids issues with the window size being larger than the signal itself.

### `if window_len < 3: return x`
- **Purpose**: If the window length is less than 3, no smoothing is applied, and the original signal is returned.
- **Example**: 
  - Calling `smooth(x, window_len=2)` would return `x` unchanged.
  - **Use Case**: Handles edge cases where smoothing isn’t practical.

### `if not window in ['flat', 'hanning', 'hamming', 'bartlett', 'blackman']: raise ValueError, "Window is one of 'flat', 'hanning', 'hamming', 'bartlett', 'blackman'"`
- **Purpose**: Checks if the specified window type is valid. If not, it raises an error.
- **Example**: 
  - Passing `window='gaussian'` would trigger the error since it’s not one of the allowed types.
  - **Use Case**: Ensures only supported window functions are used.

### `s = numpy.r_[x[window_len-1:0:-1], x, x[-2:-window_len-1:-1]]`
- **Purpose**: Extends the signal `x` by reflecting its edges to reduce boundary effects during convolution.
- **Explanation**: 
  - `x[window_len-1:0:-1]` creates a reflected copy of the start of the signal.
  - `x[-2:-window_len-1:-1]` creates a reflected copy of the end of the signal.
  - **Use Case**: Prepares the signal for convolution by reducing artifacts at the boundaries.

### `if window == 'flat': w = numpy.ones(window_len, 'd')`
- **Purpose**: If the window type is 'flat', a moving average window (an array of ones) is created.
- **Explanation**: 
  - `numpy.ones(window_len, 'd')` creates an array of ones with the length `window_len`.
  - **Use Case**: Implements simple moving average smoothing.

### `else: w = eval('numpy.' + window + '(window_len)')`
- **Purpose**: For other window types, the corresponding window function from NumPy is used.
- **Explanation**: 
  - `eval('numpy.' + window + '(window_len)')` dynamically calls the appropriate NumPy window function.
  - **Use Case**: Applies the specified window function for smoothing.

### `y = numpy.convolve(w / w.sum(), s, mode='valid')`
- **Purpose**: Convolves the prepared signal `s` with the window `w` to produce the smoothed signal `y`.
- **Explanation**: 
  - `w / w.sum()` normalizes the window.
  - `numpy.convolve(..., mode='valid')` performs the convolution operation.
  - **Use Case**: Core operation that smooths the signal by averaging it over the window.

### `return y`
- **Purpose**: Returns the smoothed signal.
- **Example**: 
  - The smoothed signal `y` is the output of the `smooth` function.
  - **Use Case**: Provides the final smoothed signal to the user.

### `from numpy import *`
- **Purpose**: Imports all functions and classes from NumPy.
- **Use Case**: Simplifies the syntax by allowing direct access to NumPy functions without the `numpy.` prefix.

### `from pylab import *`
- **Purpose**: Imports all functions and classes from `pylab`, a module that combines Matplotlib with NumPy.
- **Use Case**: Used for plotting and visualizing data.

### `def smooth_demo():`
- **Purpose**: Defines a demonstration function that shows the effect of the `smooth` function using different window types.

### `t = linspace(-4, 4, 100)`
- **Purpose**: Creates an array of 100 equally spaced values between -4 and 4.
- **Example**: 
  - `linspace(-4, 4, 100)` results in an array like `[-4.0, -3.92, ..., 3.92, 4.0]`.
  - **Use Case**: Commonly used to create a range of values for plotting.

### `x = sin(t)`
- **Purpose**: Computes the sine of each value in `t`, producing a smooth sine wave.
- **Example**: 
  - For `t = [0, pi/2, pi]`, `x` would be `[0, 1, 0]`.
  - **Use Case**: Generates a periodic signal for demonstration.

### `xn = x + randn(len(t)) * 0.1`
- **Purpose**: Adds random noise to the sine wave, simulating a noisy signal.
- **Example**: 
  - If `x = [0, 1, 0]`, `xn` might be `[0.02, 0.98, -0.01]`.
  - **Use Case**: Demonstrates the effect of smoothing on noisy data.

### `y = smooth(x)`
- **Purpose**: Applies the `smooth` function to the clean sine wave.
- **Use Case**: Provides a baseline for comparison with the noisy signal.

### `ws = 31`
- **Purpose**: Defines the window size for plotting the window functions.
- **Use Case**: Used in visualizing different window functions.

### `subplot(211)`
- **Purpose**: Creates a subplot in the figure for displaying the window functions.
- **Use Case**: Allows for multiple plots in a single figure.

### `plot(ones(ws))`
- **Purpose**: Plots a flat window (moving average) of size `ws`.
- **Use Case**: Visualizes how a flat window looks.

### `windows = ['flat', 'hanning', 'hamming', 'bartlett', 'blackman']`
- **Purpose**: Defines a list of window types to be plotted.
- **Use Case**: Used for iterating and plotting different window functions.

### `hold(True)`
- **Purpose**: Keeps the current plot so that subsequent plots are added to it.
- **Use Case**: Allows multiple plots on the same axes.

### `for w in windows[1:]: eval('plot(' + w + '(ws))')`
- **Purpose**: Iterates over the window types (excluding 'flat') and plots them.
- **Example**: 
  - For `w = 'hanning'`, it plots the Hanning window.
  - **Use Case**: Visualizes and compares different window functions.

### `axis([0,

 30, 0, 1.1])`
- **Purpose**: Sets the axis limits for the plot.
- **Use Case**: Controls the view range for better visualization.

### `legend(windows)`
- **Purpose**: Adds a legend to the plot indicating the window types.
- **Use Case**: Helps identify which line corresponds to which window.

### `title("The smoothing windows")`
- **Purpose**: Sets the title of the subplot displaying the window functions.
- **Use Case**: Provides context for the viewer.

### `subplot(212)`
- **Purpose**: Creates another subplot for displaying the signals.
- **Use Case**: Separates the plots for clarity.

### `plot(x)`
- **Purpose**: Plots the original sine wave.
- **Use Case**: Baseline signal for comparison.

### `plot(xn)`
- **Purpose**: Plots the noisy sine wave.
- **Use Case**: Demonstrates the effect of noise.

### `for w in windows: plot(smooth(xn, 10, w))`
- **Purpose**: Iterates over the window types and plots the smoothed noisy signal for each.
- **Use Case**: Shows how different window types affect the smoothing process.

### `l = ['original signal', 'signal with noise']`
- **Purpose**: Initializes the legend list with labels for the original and noisy signals.
- **Use Case**: Prepares the legend for the next plot.

### `l.extend(windows)`
- **Purpose**: Adds the window types to the legend list.
- **Use Case**: Completes the legend for the plot.

### `legend(l)`
- **Purpose**: Adds the complete legend to the signal plot.
- **Use Case**: Helps identify the different lines on the plot.

### `title("Smoothing a noisy signal")`
- **Purpose**: Sets the title of the subplot displaying the signals.
- **Use Case**: Provides context for the viewer.

### `show()`
- **Purpose**: Displays the plots.
- **Use Case**: Finalizes the visualization process.

### `if __name__ == '__main__': smooth_demo()`
- **Purpose**: If the script is run directly (not imported as a module), it calls the `smooth_demo()` function to execute the demonstration.
- **Use Case**: Common Python practice to allow code to be used both as a module and as a standalone script.


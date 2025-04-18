<h1><div align ="center">Getting Started with Images</div></h1>

`Warning`

This tutorial can contain obsolete information.

## Goal

In this tutorial you will learn how to:

- Read an image from file (using cv::imread)<br>
  `Loads an image from a file.`
- Display an image in an OpenCV window (using cv::imshow)<br>
  `Displays an image in the specified window.`
- Write an image to a file (using cv::imwrite)<br>
  `Saves an image to a specified file.`

## Source Code

```C++
#include <opencv2/core.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>

#include <iostream>

using namespace cv;

int main()
{
std::string image_path = samples::findFile("starry_night.jpg");
Mat img = imread(image_path, IMREAD_COLOR);

if(img.empty())
{
std::cout << "Could not read the image: " << image_path << std::endl;
return 1;
}

imshow("Display window", img);
int k = waitKey(0); // Wait for a keystroke in the window

if(k == 's')
{
imwrite("starry_night.png", img);
}

return 0;
}
```

## Explanation

In OpenCV 3 we have `multiple modules`. Each one takes care of a different area or approach towards image processing. You could already observe this in the structure of the user guide of these tutorials itself.
Before you use any of them you `first need to include the header files where the content of each individual module is declared`.

You'll almost always end up using the:

- `core` section, as here are defined the basic building blocks of the library
- `imgcodecs` module, which provides functions for reading and writing
- `highgui` module, as this contains the functions to show an image in a window

We also include the iostream to facilitate console line output and input.

By declaring `using namespace cv;`, in the following,
the library functions can be accessed without clearly stating the namespace.

```C++
#include <opencv2/core.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>

#include <iostream>

using namespace cv;
```

Now, let's analyze the main code. As a first step, we read the image
`"starry_night.jpg"` from the `OpenCV samples`.
In order to do so, a call to the `cv::imread` function loads the image
using the file path specified by the `first argument`.
`The second argument` is optional and specifies the format in which we want
the image. This may be:

- `IMREAD_COLOR` loads the image in the `BGR 8-bit` format. This is the default that is used here.
- `IMREAD_UNCHANGED` loads the image as is (including the alpha channel if present)
  `The alpha channel is a special channel that handles transparency. It is an 8-bit layer, in addition to the red, green,
and blue (RGB) channels, that defines opacity levels for each pixel in the image. `
- `IMREAD_GRAYSCALE` loads the image as an intensity one

After reading in the image data will be stored in a `cv::Mat` object.<br>
A `cv::Mat` is a `class` in the OpenCV library that represents a matrix or an image. It’s a basic image container that can store image data,
such as pixel values, and provides various functions for image processing.

```C++
 std::string image_path = samples::findFile("starry_night.jpg");
 Mat img = imread(image_path, IMREAD_COLOR);
```

## Note

OpenCV offers support for the image formats Windows bitmap (bmp), portable image formats (pbm, pgm, ppm) and Sun raster (sr, ras). With help of plugins (you need to specify to use them if you build yourself the library, nevertheless in the packages we ship present by default) you may also load image formats like JPEG (jpeg, jpg, jpe), JPEG 2000 (jp2 - codenamed in the CMake as Jasper), TIFF files (tiff, tif) and portable network graphics (png). Furthermore, OpenEXR is also a possibility.

Afterwards, a check is executed, if the image was loaded correctly.

```C++
 if(img.empty())
 {
 std::cout << "Could not read the image: " << image_path << std::endl;
 return 1;
 }
```

Then, the image is shown using a call to the `cv::imshow` function. The `first argument` is the title of the window and the `second argument` is the `cv::Mat` object that will be shown.

`cv::imshow` is a function in OpenCV that displays an image in a window.
It is a part of the `High-level GUI module` in OpenCV.

**The function takes two parameters:**the name of the window and the image to be displayed.

Here’s a breakdown of the function’s parameters and behavior:

- `window_name`: The name of the window in which the image will be displayed.
  This can be a string representing any name.
- `image`: The image to be displayed. This can be any type of image supported by OpenCV, such as a `cv2.Mat` object, a `cv2.UMat` object, or a `cv2.cuda.GpuMat` object.
  If the window was created with the `cv::WINDOW_AUTOSIZE` flag, the image will be displayed with its original size, but it will still be limited by the screen resolution. Otherwise, the image will be scaled to fit the window.

Here’s an example of how to use cv::imshow:

In Python

```.py
import cv2

//Load the image
img = cv2.imread('image.jpg')

/Display the image
cv2.imshow('Window Name', img)
```

In Cpp

```.cpp
#include <opencv2/opencv.hpp>

int main() {
    // Load the image
    cv::Mat img = cv::imread("image.jpg");

    // Check if the image was loaded successfully
    if (img.empty()) {
        std::cerr << "Error: Could not load image" << std::endl;
        return -1;
    }

    // Display the image
    cv::imshow("Window Name", img);

    // Wait for a key press indefinitely
    cv::waitKey(0);

    return 0;
}
```

### Explanation

```cpp
#include <opencv2/opencv.hpp>
```

This line includes the OpenCV header file which provides the necessary functions and data structures for image processing.

```cpp
int main() {
```

This is the main function, the entry point of the C++ program.

```cpp
    // Load the image
    cv::Mat img = cv::imread("image.jpg");
```

- `cv::Mat`: This is a matrix data type provided by OpenCV, used to store images.
- `cv::imread("image.jpg")`: This function reads an image from the file named "image.jpg" and stores it in the `img` matrix.

```cpp
    // Check if the image was loaded successfully
    if (img.empty()) {
        std::cerr << "Error: Could not load image" << std::endl;
        return -1;
    }
```

- `img.empty()`: This method checks if the image matrix is empty, which would indicate that the image failed to load.
- `std::cerr`: This is the standard error stream, used here to print an error message.
- `return -1;`: This terminates the program with a status code of -1, indicating an error.

```cpp
    // Display the image
    cv::imshow("Window Name", img);
```

- `cv::imshow("Window Name", img)`: This function displays the image in a window. The first parameter is the name of the window, and the second is the image matrix to display.

```cpp
    // Wait for a key press indefinitely
    cv::waitKey(0);
```

- `cv::waitKey(0)`: This function waits for a key press. The parameter `0` means it will wait indefinitely until a key is pressed.

```cpp
    return 0;
}
```

- `return 0;`: This terminates the program with a status code of 0, indicating successful completion.

`Note` :

`cv::imshow` does not return any value.
After displaying the image, you need to call `cv2.waitKey` or `cv2.pollKey`
to perform GUI housekeeping tasks and make the window respond to mouse and
keyboard events.

Also, if you need to show an image that is bigger than the screen resolution,
you need to call `cv2.namedWindow` with the `cv::WINDOW_NORMAL` flag before
calling `cv2.imshow`.

Because we want our window to be displayed until the user presses a key
(otherwise the program would end far too quickly), we use the `cv::waitKey`
function whose only parameter is just how long should it wait for a user input
(measured in milliseconds). `Zero means to wait forever`.
The return value is the key that was pressed.

```C++
 imshow("Display window", img);
 int k = waitKey(0); // Wait for a keystroke in the window
```

In the end, the image is written to a file if the pressed key was the `"s"-key`.
For this the `cv::imwrite` function is called that has the file path and
the `cv::Mat` object as an argument.

```C++
 if(k == 's')
 {
 imwrite("starry_night.png", img);
 }
```

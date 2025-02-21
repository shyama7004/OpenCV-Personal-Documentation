<h1 align="center">Getting Started with Images</h1>

**Warning:** This tutorial may contain outdated info.

## Goal

Learn how to:

- **Read an image** from a file using `cv::imread`
  *(Loads an image from a file.)*
- **Display an image** in a window using `cv::imshow`
  *(Shows an image in the specified window.)*
- **Save an image** to a file using `cv::imwrite`
  *(Writes an image to a file.)*

## Source Code

```C++
#include <opencv2/core.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>
#include <iostream>

using namespace cv;

int main()
{
    // Find and load the image
    std::string image_path = samples::findFile("starry_night.jpg");
    Mat img = imread(image_path, IMREAD_COLOR);

    // Check if the image loaded successfully
    if(img.empty())
    {
        std::cout << "Could not read the image: " << image_path << std::endl;
        return 1;
    }

    // Display the image in a window
    imshow("Display window", img);
    int k = waitKey(0); // Wait for a key press

    // Save the image if the 's' key is pressed
    if(k == 's')
    {
        imwrite("starry_night.png", img);
    }

    return 0;
}
```

## Explanation

1. **Reading the Image**
   The function `imread` loads the image from the file path.
   - `IMREAD_COLOR` loads the image in color.
   - You can use `IMREAD_GRAYSCALE` for a grayscale image or `IMREAD_UNCHANGED` to keep the original format.

2. **Displaying the Image**
   The `imshow` function opens a window to display the image.
   - The first argument is the window title.
   - The second argument is the image (`cv::Mat`).

3. **Saving the Image**
   The `imwrite` function saves the image to a file.
   - Here, if you press the "s" key, the image is saved as "starry_night.png".

## Enhancements

- **Clear Error Handling:**
  The code checks if the image is loaded correctly and prints an error message if not.

- **Simple and Concise Comments:**
  Comments are added to explain each step clearly without unnecessary details.

- **Straightforward Structure:**
  The tutorial focuses on the basics—reading, displaying, and saving an image—making it easier to follow.

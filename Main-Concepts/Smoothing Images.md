# Goal

In this tutorial, you will learn how to apply diverse linear filters to smooth images using OpenCV functions such as:

- `blur()`
- `GaussianBlur()`
- `medianBlur()`
- `bilateralFilter()`

- For more details about linear filter click on this link : [Click here](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/More%20Explanation/3.9.1.md)
# Theory

> Note: The explanation below belongs to the book *Computer Vision: Algorithms and Applications* by Richard Szeliski and to *Learning OpenCV*.

- Smoothing, also called blurring, is a simple and frequently used image processing operation.
- There are many reasons for smoothing. In this tutorial, we will focus on smoothing to reduce noise (other uses will be seen in the following tutorials).
- To perform a smoothing operation, we will apply a filter to our image. The most common type of filters are linear, in which an output pixel's value(i.e.<em>g(i,j)</em>) is determined as a weighted sum of input pixel values(i.e.<em>f(i+k,j+l)</em>):

![img1](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/13.png)

Here,  <em>h(i,j)</em> is called the kernel, which is the coefficients of the filter.

It helps to visualize a filter as a window of coefficients sliding across the image.

- There are many kinds of filters. Here we will mention the most used:

## Normalized Box Filter

- This filter is the simplest of all! Each output pixel is the mean of its kernel neighbors (all of them contribute with equal weights).
- The kernel is below:

![img2](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/14.png)

## Gaussian Filter

- Probably the most useful filter (`although not the fastest`). Gaussian filtering is done by convolving each point in the input array with a Gaussian kernel and then summing them all to produce the output array. 

- Just to make the picture clearer, remember how a 1D Gaussian kernel looks like:

![img2](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/16.png)


Assuming that an image is 1D, you can notice that the pixel located in the middle would have the biggest weight. The weight of its neighbors decreases as the spatial distance between them and the center pixel increases.

**Note:** Remember that a 2D Gaussian can be represented as:

![img2](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/15.png)


## Median Filter

The median filter runs through each element of the signal (in this case, the image) and replaces each pixel with the median of its neighboring pixels (located in a square neighborhood around the evaluated pixel).

## Bilateral Filter

- So far, we have explained some filters whose main goal is to smooth an input image. However, sometimes the filters not only dissolve the noise but also smooth away the edges. To avoid this (to a certain extent at least), we can use a bilateral filter.

- In an analogous way to the Gaussian filter, the bilateral filter also considers the neighboring pixels with weights assigned to each of them. These weights have two components, the first of which is the same weighting used by the Gaussian filter. The second component takes into account the difference in intensity between the neighboring pixels and the evaluated one.

- For a more detailed explanation, you can check [this link](https://docs.opencv.org/3.4/d4/d13/tutorial_py_filtering.html).

# Code

## What does this program do?

- Loads an image.
- Applies 4 different kinds of filters (explained in Theory) and shows the filtered images sequentially.

### Downloadable code: [Click here](https://github.com/opencv/opencv)

### Code at a glance:

```cpp
#include <iostream>
#include "opencv2/imgproc.hpp"
#include "opencv2/imgcodecs.hpp"
#include "opencv2/highgui.hpp"

using namespace std;
using namespace cv;

int DELAY_CAPTION = 1500;
int DELAY_BLUR = 100;
int MAX_KERNEL_LENGTH = 31;

Mat src; Mat dst;
char window_name[] = "Smoothing Demo";

int display_caption( const char* caption );
int display_dst( int delay );


int main( int argc, char ** argv )
{
    namedWindow( window_name, WINDOW_AUTOSIZE );

    const char* filename = argc >=2 ? argv[1] : "lena.jpg";

    src = imread( samples::findFile( filename ), IMREAD_COLOR );
    if (src.empty())
    {
        printf(" Error opening image\n");
        printf(" Usage:\n %s [image_name-- default lena.jpg] \n", argv[0]);
        return EXIT_FAILURE;
    }

    if( display_caption( "Original Image" ) != 0 )
    {
        return 0;
    }

    dst = src.clone();
    if( display_dst( DELAY_CAPTION ) != 0 )
    {
        return 0;
    }

    if( display_caption( "Homogeneous Blur" ) != 0 )
    {
        return 0;
    }

    for ( int i = 1; i < MAX_KERNEL_LENGTH; i = i + 2 )
    {
        blur( src, dst, Size( i, i ), Point(-1,-1) );
        if( display_dst( DELAY_BLUR ) != 0 )
        {
            return 0;
        }
    }

    if( display_caption( "Gaussian Blur" ) != 0 )
    {
        return 0;
    }

    for ( int i = 1; i < MAX_KERNEL_LENGTH; i = i + 2 )
    {
        GaussianBlur( src, dst, Size( i, i ), 0, 0 );
        if( display_dst( DELAY_BLUR ) != 0 )
        {
            return 0;
        }
    }

    if( display_caption( "Median Blur" ) != 0 )
    {
        return 0;
    }

    for ( int i = 1; i < MAX_KERNEL_LENGTH; i = i + 2 )
    {
        medianBlur ( src, dst, i );
        if( display_dst( DELAY_BLUR ) != 0 )
        {
            return 0;
        }
    }

    if( display_caption( "Bilateral Blur" ) != 0 )
    {
        return 0;
    }

    for ( int i = 1; i < MAX_KERNEL_LENGTH; i = i + 2 )
    {
        bilateralFilter ( src, dst, i, i*2, i/2 );
        if( display_dst( DELAY_BLUR ) != 0 )
        {
            return 0;
        }
    }

    display_caption( "Done!" );

    return 0;
}

int display_caption( const char* caption )
{
    dst = Mat::zeros( src.size(), src.type() );
    putText( dst, caption,
             Point( src.cols/4, src.rows/2),
             FONT_HERSHEY_COMPLEX, 1, Scalar(255, 255, 255) );

    return display_dst(DELAY_CAPTION);
}

int display_dst( int delay )
{
    imshow( window_name, dst );
    int c = waitKey ( delay );
    if( c >= 0 ) { return -1; }
    return 0;
}
```

## Explanation

Let's check the OpenCV functions that involve only the smoothing procedure, as the rest is already known by now.

### Normalized Block Filter:

OpenCV offers the function `blur()` to perform smoothing with this filter. We specify 4 arguments:

- `src`: Source image
- `dst`: Destination image
- `Size(w, h)`: Defines the size of the kernel to be used (of width `w` pixels and height `h` pixels)
- `Point(-1, -1)`: Indicates where the anchor point (the pixel evaluated) is located with respect to the neighborhood. If there is a negative value, then the center of the kernel is considered the anchor point.

```cpp
for ( int i = 1; i < MAX_KERNEL_LENGTH; i = i + 2 )
{
    blur( src, dst, Size( i, i ), Point(-1,-1) );
    if( display_dst( DELAY_BLUR ) != 0 )
    {
        return 0;
    }
}
```

### Gaussian Filter:

It is performed by the function `GaussianBlur()`. Here we use 4 arguments:

- `src`: Source image
- `dst`: Destination image
- `Size(w, h)`: The size of the kernel to be used (the neighbors to be considered). `w` and `h` have to be odd and positive numbers; otherwise, the size will be calculated using the `sigmaX` and `sigmaY` arguments.
- `sigmaX`: The standard deviation in x. Writing `0` implies that `sigmaX` is calculated using kernel size.
- `sigmaY`: The standard deviation in y. Writing `0` implies that `sigmaY` is calculated using kernel size.

```cpp
for ( int i = 1; i < MAX_KERNEL_LENGTH; i = i + 2 )
{
    GaussianBlur( src, dst, Size( i, i


# Bilateral Filtering for Gray and Color Images

## Introduction

Filtering is perhaps the most fundamental operation of image processing and computer vision. In the broadest sense of the term "filtering," the value of the filtered image at a given location is a function of the values of the input image in a small neighborhood of the same location.

![img3](https://imgs.search.brave.com/SftRo8U_G7ybG547leuvL7ehw0_W6-Gmz9_hkkwStz8/rs:fit:860:0:0:0/g:ce/aHR0cHM6Ly9tZWRp/YS5nZWVrc2Zvcmdl/ZWtzLm9yZy93cC1j/b250ZW50L3VwbG9h/ZHMvMjAyNDA1MDkx/NzQxMDQvRnVuY3Rp/b24taW4tTWF0aHMu/d2VicA)

 For example, Gaussian low-pass filtering computes a weighted average of pixel values in the neighborhood, in which the weights decrease with distance from the neighborhood center. Although formal and quantitative explanations of this weight fall-off can be given, the intuition is that images typically vary slowly over space, so near pixels are likely to have similar values, and it is therefore appropriate to average them together. The noise values that corrupt these nearby pixels are mutually less correlated than the signal values, so noise is averaged away while the signal is preserved.

The assumption of slow spatial variations fails at edges, which are consequently blurred by linear low-pass filtering. 
- How can we prevent averaging across edges while still averaging within smooth regions?
- Many efforts have been devoted to reducing this undesired effect.
- Bilateral filtering is a simple, non-iterative scheme for edge-preserving smoothing.


## The Idea

- The basic idea underlying bilateral filtering is to do in the `range` of an image what traditional filters do in its `domain`.
- Two pixels can be close to one another, that is, occupy a nearby spatial location, or they can be similar to one another, that is, have nearby values, possibly in a perceptually meaningful fashion.

Consider a shift-invariant low-pass domain filter applied to an image:

<img src="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image514.gif" width =400 height = 60>

**f** and **h** emphasize the fact that both input and output images may be multi-band. In order to preserve the DC component, it must be:

<img src="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image746.gif" width =400 height = 60>

Range filtering is similarly defined:

<img src="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image397.gif" width =400 height = 60>


In this case, the kernel measures the photometric similarity(measure of intensity similarity or brightness similarity) between pixels. The normalization constant in this case is:

<img src="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image053.gif" width =400 height = 60>

Range filtering on its own doesn't consider the spatial distribution of image intensities, meaning it doesn't account for where the intensities are located in the image. Combining intensities from across the entire image doesn't make sense because values far from a specific point shouldn't influence that point's final value. Moreover, using range filtering alone only changes the image's color map without being particularly useful. The right approach is to combine domain and range filtering, which ensures that both the geometric position and color similarity are considered. This combined filtering approach is described as follows:

<img src="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image477.gif" width =400 height = 60>

With the normalization:

<img src ="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image788.gif" width =400 height =60>

Bilateral filtering combines both domain and range filtering. It replaces the pixel value at a specific location with an average of nearby and similar pixel values. In smooth areas of an image, where pixel values are similar, the bilateral filter works like a standard filter, averaging out small differences caused by noise. However, at sharp boundaries, like in Figure 1(a), between a dark and bright region, the filter behaves differently, helping to preserve those edges while still smoothing the image.

<img src ="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/17.png">


When the bilateral filter focuses on a pixel on the bright side of a boundary, it mainly considers nearby bright pixels, giving them more weight, while largely ignoring the darker ones. This is because the similarity function, which determines how much influence nearby pixels have, gives values close to one for similar pixels and close to zero for different ones. In Figure 1(b), this function is shown for a 23x23 filter centered near the step in Figure 1(a). The normalization term \( k(x) \) makes sure that all the pixel weights add up to one. As a result, the filter averages the bright pixels and ignores the dark ones. Similarly, when centered on a dark pixel, the bright pixels are ignored. This method allows the filter to handle edges well, preserving sharp boundaries and ensuring good filtering, as shown in Figure 1(c).

[Back to Index](#bilateral-filtering-for-gray-and-color-images)

## The Gaussian Case

A simple and important case of bilateral filtering is shift-invariant Gaussian filtering, in which both the closeness function c and the similarity function s are Gaussian functions of the Euclidean distance between their arguments. More specifically, c is radially symmetric:
<img src="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image548.gif" width =400 height =60>

Where:

<img src="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image710.gif" width =400 height=60>

is the Euclidean distance. The similarity function s is perfectly analogous to c:

<img src="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image865.gif" width =400 height =60>

Where:

<img src="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image347.gif" width =400 height =60>

is a suitable measure of distance in intensity space. In the scalar case, this may be simply the absolute difference of the pixel difference or, since noise increases with image intensity, an intensity-dependent version of it. Just as this form of domain filtering is shift-invariant, the Gaussian range filter introduced above is insensitive to overall additive changes of image intensity. Of course, the range filter is shift-invariant as well.

[Back to Index](#bilateral-filtering-for-gray-and-color-images)

## Experiments with Black-and-White Images

Figure 2 (a) and (b) show the potential of bilateral filtering for the removal of texture. The picture "simplification" illustrated by figure 2 (b) can be useful for data reduction without loss of overall shape features in applications such as image transmission, picture editing, and manipulation, image description for retrieval.

![Figure 2](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/18.png)

**Figure 2**
This paragraph describes the effects of bilateral filtering on an image. When applying bilateral filtering with parameters **sd = 3** pixels (where "sd" stands for the spatial domain, controlling the influence of neighboring pixels) and **sr = 50** intensity values (where "sr" stands for the range domain, controlling the influence of pixel intensity differences), most of the fine details or textures in the image are smoothed out, while the edges remain sharp. 

In Figure 3, you can see that the onions in the image lose their fine texture but still maintain clear boundaries. The overall shading is also kept intact, which means that the main structure and shapes in the image are preserved, giving the onions a more graphic-like appearance.

<img src="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/19.png" width =1000 height =700>

**Figure 3**

[Back to Index](#bilateral-filtering-for-gray-and-color-images)

## Experiments with Color Images

In black-and-white images, smoothing usually just creates shades of gray between different levels, which can blur the edges. But with color images, things get trickier. Between any two colors, like blue and red, there can be other colors, such as pink and purple. When you smooth these images, it can create unwanted color bands along the edges. So, the image not only looks blurry but also has strange, colorful outlines around objects.

<img src="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/20.png" width =700 height =600>


**Figure 4**

Figure 4 (a) shows a picture with a red jacket against a blue sky. Even though the image isn't blurred, you can see a thin pink-purple line where the red and blue meet. This line is caused by lens blurring and pixel averaging, where the colors of the red jacket and blue sky mix together, creating a pink color. When the image is smoothed, this pink-purple area becomes even more noticeable, as seen in figure 4 (b).

To fix this, we could smooth the red, green, and blue parts of the image separately, but this often makes the pink-purple band even more obvious, as shown in figure 4 (c). However, using bilateral filtering gives a much better result. This method combines the three color parts of the image in a way that accurately measures the differences between colors. By using a color space designed to match how humans see color differences, bilateral filtering only averages together similar colors and keeps important edges sharp. In figure 4 (d), you can see that the pink band has shrunk a lot, and no strange colors have been added.
<img src="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/21.png" width =1200 height =300>

**Figure 5**

Figure 5 (c) shows what happens after applying bilateral filtering five times to the image in figure 5 (a). Even after just one pass, the image looks much cleaner, as seen in figure 5 (b), which is often good enough for most purposes. However, when you apply the filter multiple times, it flattens the colors even more but still keeps the edges sharp. The final image uses fewer colors, making the effects of the filtering more noticeable, especially when printed. The result, as shown in figure 5 (c), has a cartoon-like look, where shadows and edges are preserved, but most of the shading is removed, and no new colors are added.

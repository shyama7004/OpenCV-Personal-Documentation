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

The spatial distribution of image intensities plays no role in range filtering taken by itself. Combining intensities from the entire image, however, makes little sense since the distribution of image values far away from x ought not to affect the final value at x. In addition, one can show that range filtering without domain filtering merely changes the color map of an image and is therefore of little use. The appropriate solution is to combine domain and range filtering, thereby enforcing both geometric and photometric locality. Combined filtering can be described as follows:

<img src="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image477.gif" width =400 height = 60>

With the normalization:

<img src ="https://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/MANDUCHI1/image788.gif" width =400 height =60>

Combined domain and range filtering will be denoted as bilateral filtering. It replaces the pixel value at x with an average of similar and nearby pixel values. In smooth regions, pixel values in a small neighborhood are similar to each other, and the bilateral filter acts essentially as a standard domain filter, averaging away the small, weakly correlated differences between pixel values caused by noise. Consider now a sharp boundary between a dark and a bright region, as in figure 1(a).

<img src ="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/17.png">


When the bilateral filter is centered, say, on a pixel on the bright side of the boundary, the similarity function s assumes values close to one for pixels on the same side, and values close to zero for pixels on the dark side. The similarity function is shown in figure 1(b) for a 23x23 filter support centered two pixels to the right of the step in figure 1(a). The normalization term k(x) ensures that the weights for all the pixels add up to one. As a result, the filter replaces the bright pixel at the center by an average of the bright pixels in its vicinity, and essentially ignores the dark pixels. Conversely, when the filter is centered on a dark pixel, the bright pixels are ignored instead. Thus, as shown in figure 1(c), good filtering behavior is achieved at the boundaries, thanks to the domain component of the filter, and crisp edges are preserved at the same time, thanks to the range component.

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

Bilateral filtering with parameters **sd = 3** pixels and **sr = 50** intensity values is applied to the image in figure 3 (a) to yield the image in figure 3 (b). Notice that most of the fine texture has been filtered away, and yet all contours are as crisp as in the original image. Figure 3 (c) shows a detail of figure 3 (a), and figure 3 (d) shows the corresponding filtered version. The two onions have assumed a graphics-like appearance, and the fine texture has gone. However, the overall shading is preserved, because it is well within the band of the domain filter and is almost unaffected by the range filter. Also, the boundaries of the onions are preserved.

<img src="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/19.png" width =1000 height =700>

**Figure 3**

[Back to Index](#bilateral-filtering-for-gray-and-color-images)

## Experiments with Color Images

For black-and-white images, intensities between any two gray levels are still gray levels. As a consequence, when smoothing black-and-white images with a standard low-pass filter, intermediate levels of gray are produced across edges, thereby producing blurred images. With color images, an additional complication arises from the fact that between any two colors there are other, often rather different colors. For instance, between blue and red there are various shades of pink and purple. Thus, disturbing color bands may be produced when smoothing across color edges. The smoothed image does not just look blurred, it also exhibits odd-looking, colored auras around objects.

<img src="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/20.png" width =700 height =600>

**Figure 4**

Figure 4 (a) shows a detail from a picture with a red jacket against a blue sky. Even in this unblurred picture, a thin pink-purple line is visible, and is caused by a combination of lens blurring and pixel averaging. In fact, pixels along the boundary, when projected back into the scene, intersect both red jacket and blue sky, and the resulting color is the pink average of red and blue. When smoothing, this effect is emphasized, as the broad, blurred pink-purple area in figure 4 (b) shows.

To address this difficulty, edge-preserving smoothing could be applied to the red, green, and blue components of the image separately. However, the intensity profiles across the edge in the three color bands are in general different. Smoothing the three color bands separately results in an even more pronounced pink and purple band than in the original, as shown in figure 4 (c). The pink-purple band, however, is not widened as in the standard-blurred version of figure 4 (b).

A much better result can be obtained with bilateral filtering. In fact, a bilateral filter allows combining the three color bands appropriately, and measuring photometric distances between pixels in the combined space. Moreover, this combined distance can be made to correspond closely to perceived dissimilarity by using Euclidean distance in the CIE-Lab color space. This color space is based on a large body of psychophysical data concerning color-matching experiments performed by human observers. In this space, small Euclidean distances are designed to correlate strongly with the perception of color discrepancy as experienced by an "average" color-normal human observer. Thus, in a sense, bilateral filtering performed in the CIE-Lab color space is the most natural type of filtering for color images: only perceptually similar colors are averaged together, and only perceptually important edges are preserved. Figure 4 (d) shows the image resulting from bilateral smoothing of the image in figure 4 (a). The pink band has shrunk considerably, and no extraneous colors appear.

<img src="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Images/19.png" width =1200 height =300>

**Figure 5**

Figure 5 (c) shows the result of five iterations of bilateral filtering of the image in figure 5 (a). While a single iteration produces a much cleaner image (figure 5 (b)) than the original, and is probably sufficient for most image processing needs, multiple iterations have the effect of flattening the colors in an image considerably, but without blurring edges. The resulting image has a much smaller color map, and the effects of bilateral filtering are easier to see when displayed on a printed page. Notice the cartoon-like appearance of figure 5 (c). All shadows and edges are preserved, but most of the shading is gone, and no "new" colors are introduced by filtering.

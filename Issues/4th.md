Target issues 

### Issue Link : [Click-Here](https://github.com/opencv/opencv/issues/25974)
### Issue Link : [Click-Here](https://github.com/opencv/opencv/issues/25882)
### Issue Link : [Click-Here](https://github.com/opencv/opencv/issues/26315)

<details>
  <summary>Detailed explanation of Issue 1(Just for reassurance)</summary>

- Includes the necessary OpenCV headers for image processing and feature detection.

**Test Case Definition:**

```cpp
TEST(Features2D_ORB, MaskValue)
{
```

- Defines a test case named `MaskValue` within the `Features2D_ORB` test fixture. This is typically used in unit testing frameworks like Google Test to organize and run tests.

**Image Loading:**

```cpp
Mat gray = imread(cvtest::findDataFile("features2d/tsukuba.png"), IMREAD_GRAYSCALE);
ASSERT_FALSE(gray.empty());
```

- Loads the grayscale image "tsukuba.png" from the OpenCV test data directory.
- Checks if the image was loaded successfully and asserts that it's not empty.

**Region of Interest (ROI) Definition:**

```cpp
cv::Rect roi(gray.cols/4, gray.rows/4, gray.cols/2, gray.rows/2);
```

- Creates a rectangular region of interest (ROI) within the image. The ROI covers the central half of the image.

**Mask Creation:**

```cpp
Mat mask255 = Mat::zeros(gray.size(), CV_8UC1);
mask255(roi).setTo(255);

Mat mask1 = Mat::zeros(gray.size(), CV_8UC1);
mask1(roi).setTo(1);
```

- Creates two masks of the same size as the image, initialized with zeros.
- Sets the pixels within the ROI in `mask255` to 255 (white) and in `mask1` to 1.

**ORB Detector Creation:**

```cpp
Ptr<ORB> orb = cv::ORB::create();
```

- Creates an ORB (Oriented FAST and Rotated BRIEF) feature detector object.

**Feature Detection and Description:**

```cpp
vector<KeyPoint> keypoints_mask255;
Mat descriptors_mask255;
orb->detectAndCompute(gray, mask255, keypoints_mask255, descriptors_mask255, false);
```

- Detects keypoints and computes their descriptors using the ORB algorithm on the grayscale image `gray` with the mask `mask255`.
- Stores the detected keypoints in `keypoints_mask255` and the corresponding descriptors in `descriptors_mask255`.

**Keypoint and Descriptor Printing:**

```cpp
std::cout << "keypoints_mask255.size(): " << keypoints_mask255.size() << std::endl;
std::cout << "descriptors_mask255.size(): " << descriptors_mask255.size()  << std::endl;
```

- Prints the number of detected keypoints and descriptors for the mask `mask255`.

**Repeat with Mask1:**

- Repeats the same steps with `mask1` to detect keypoints and descriptors using a different mask.

**Descriptor Comparison:**

```cpp
Mat diff = descriptors_mask1 != descriptors_mask255;
ASSERT_EQ(countNonZero(diff), 0) << "descriptors are not identical";
```

- Compares the descriptors computed with `mask1` and `mask255`.
- Asserts that the difference between the two descriptor matrices is zero, indicating that the descriptors should be identical if the masks are equivalent.

**Overall, this code tests the behavior of the ORB algorithm when using different masks for feature detection. It verifies that the detected keypoints and descriptors should be identical if the masks are equivalent within the ROI.**

<details>
  <summary>Pointer Discussion</summary>
  
**Ptr<ORB> orb** is a smart pointer in OpenCV that represents an instance of the ORB (Oriented FAST and Rotated BRIEF) feature detector class.

### Understanding Ptr
* **Smart Pointers:** In C++, smart pointers are used to automatically manage memory. They provide features like automatic deallocation and reference counting to prevent memory leaks and dangling pointers.
* **Ptr Template:** `Ptr` is a template class in OpenCV that can be used to create smart pointers for any class.

### ORB Feature Detector
* **ORB:** Oriented FAST and Rotated BRIEF is a feature detection algorithm commonly used in computer vision. It's known for its speed and robustness.

### Ptr<ORB> Usage
* **Creating an Instance:**
  ```cpp
  Ptr<ORB> orb = ORB::create();
  ```
  - This line creates a new instance of the ORB class using the `create()` method. The `Ptr` automatically manages the memory for this instance.
* **Accessing Methods:**
  ```cpp
  orb->detectAndCompute(gray, mask, keypoints, descriptors);
  ```
  - You can use the `->` operator to access methods and properties of the ORB object through the `Ptr`. Here, `detectAndCompute` is a method of the ORB class that detects keypoints and computes their descriptors.

**Benefits of using Ptr<ORB>:**

- **Automatic memory management:** The `Ptr` will automatically deallocate the ORB object when it goes out of scope, preventing memory leaks.
- **Reference counting:** Multiple `Ptr` objects can share ownership of the same underlying object, ensuring that it's not deleted prematurely.
- **Type safety:** `Ptr` provides type safety and prevents errors related to invalid pointer usage.

By using `Ptr<ORB>`, you can safely and efficiently work with the ORB feature detector in your OpenCV applications.
</details>

</details>

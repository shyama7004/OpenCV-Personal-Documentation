Link to the issue : [#26264](https://github.com/opencv/opencv/issues/26264)
### Function Declaration
```cpp
void cv::drawContours( InputOutputArray _image, InputArrayOfArrays _contours,
                   int contourIdx, const Scalar& color, int thickness,
                   int lineType, InputArray _hierarchy,
                   int maxLevel, Point offset )
```
- **`_image`**: The image on which contours are drawn. It's an input-output array (since it's modified).
- **`_contours`**: A collection of points that represent the contours.
- **`contourIdx`**: Specifies which contour to draw. If set to -1, all contours are drawn.
- **`color`**: The color used to draw the contours.
- **`thickness`**: Thickness of the contour lines. If negative, contours are drawn as filled polygons.
- **`lineType`**: Type of line to draw, e.g., 8-connected, 4-connected, or anti-aliased.
- **`_hierarchy`**: Hierarchy of the contours, used to represent nested contours.
- **`maxLevel`**: Maximum depth of contour drawing (only draw up to this level in the hierarchy).
- **`offset`**: A point used to shift all contour points by a specific amount.

### Initial Instrumentation and Checks
```cpp
CV_INSTRUMENT_REGION();
CV_Assert( thickness <= MAX_THICKNESS );
const size_t ncontours = _contours.total();
if (!ncontours)
    return;
CV_Assert(ncontours <= (size_t)std::numeric_limits<int>::max());
if (lineType == cv::LINE_AA && _image.depth() != CV_8U)
    lineType = 8;
Mat image = _image.getMat(), hierarchy = _hierarchy.getMat();
```
- **`CV_INSTRUMENT_REGION()`**: A macro used for performance profiling in OpenCV.
- **`CV_Assert( thickness <= MAX_THICKNESS )`**: Ensures that the thickness does not exceed a predefined limit (`MAX_THICKNESS`).
- **`ncontours = _contours.total()`**: Gets the total number of contours.
- **`if (!ncontours) return;`**: If there are no contours, return early.
- **`CV_Assert(ncontours <= (size_t)std::numeric_limits<int>::max());`**: Asserts that the number of contours doesn't exceed the maximum possible integer value.
- **`lineType` adjustment**: If anti-aliased lines (`cv::LINE_AA`) are requested but the image depth is not 8-bit, the line type is set to 8.
- **`image` and `hierarchy`**: Converts `_image` and `_hierarchy` to `Mat` objects for easier manipulation.

### Drawing Contour Lines
```cpp
if (thickness >= 0) // contour lines
{
    double color_buf[4] {};
    scalarToRawData(color, color_buf, _image.type(), 0 );
    int i = 0, end = (int)ncontours;
    if (contourIdx >= 0)
    {
        i = contourIdx;
        end = i + 1;
    }
    for (; i < end; ++i)
    {
        Mat cnt = _contours.getMat(i);
        if (cnt.empty())
            continue;
        const int npoints = cnt.checkVector(2, CV_32S);
        CV_Assert(npoints > 0);
        for (int j = 0; j < npoints; ++j)
        {
            const bool isLastIter = j == npoints - 1;
            const Point pt1 = cnt.at<Point>(j);
            const Point pt2 = cnt.at<Point>(isLastIter ? 0 : j + 1);
            cv::ThickLine(image, pt1 + offset, pt2 + offset, color_buf, thickness, lineType, 2, 0);
        }
    }
}
```
<details>
  <summary>Explanation of each line of the code</summary>
  This code snippet is part of a function likely used for drawing contours in an image, typically in an image processing library like OpenCV. Here’s a line-by-line breakdown of what each part does:

### Explanation of Each Line:

1. **`if (thickness >= 0) // contour lines`**
   - **Purpose**: Checks if the `thickness` of the contour lines to be drawn is non-negative. If `thickness` is less than 0, it could be an indication that filled contours are desired (e.g., `thickness = -1` in OpenCV fills the shape).
   - **Comment**: `// contour lines` hints that this block will draw contour lines.

2. **`double color_buf[4] {};`**
   - **Purpose**: Initializes an array of four `double` elements (`color_buf`) with default values (`0`). This buffer will hold the raw color data used for drawing the contours.

3. **`scalarToRawData(color, color_buf, _image.type(), 0);`**
   - **Purpose**: Converts the given `color` (likely a `cv::Scalar`) to raw data and stores it in `color_buf`. The `scalarToRawData` function adapts the color format based on the image type (`_image.type()`), making it compatible with the underlying data format of the image.
   - **Parameters**: 
     - `color` — the input color.
     - `color_buf` — buffer where the converted color values will be stored.
     - `_image.type()` — image type (e.g., grayscale, RGB, etc.).
     - `0` — indicates no additional flags for this conversion.

4. **`int i = 0, end = (int)ncontours;`**
   - **Purpose**: Initializes two variables:
     - `i` — used to loop through the contours, starting from the first one (`0`).
     - `end` — the index marking the end of the loop, set to `ncontours` (the total number of contours).

5. **`if (contourIdx >= 0)`**
   - **Purpose**: Checks if a specific contour index (`contourIdx`) is provided. If `contourIdx` is non-negative, it indicates that only one specific contour should be drawn.

6. **`i = contourIdx;`**
   - **Purpose**: If a specific `contourIdx` is provided (from the previous `if` condition), sets `i` to this value, so only this contour is processed.

7. **`end = i + 1;`**
   - **Purpose**: Updates `end` to `i + 1`, ensuring that the loop only processes the single contour specified by `contourIdx`.

8. **`for (; i < end; ++i)`**
   - **Purpose**: A `for` loop iterating through the contours from `i` to `end`. If `contourIdx` was specified, `i` will start and end at `contourIdx`. Otherwise, it will go through all the contours.

9. **`Mat cnt = _contours.getMat(i);`**
   - **Purpose**: Retrieves the current contour (`i`-th contour) from `_contours` and stores it in a `Mat` object called `cnt`.

10. **`if (cnt.empty()) continue;`**
    - **Purpose**: Checks if the retrieved contour (`cnt`) is empty. If so, skips to the next iteration of the loop.

11. **`const int npoints = cnt.checkVector(2, CV_32S);`**
    - **Purpose**: Checks that the contour is a valid 2D point vector (`checkVector(2)`), and its elements are stored in a 32-bit signed integer format (`CV_32S`). The function returns the number of points in the contour (`npoints`).
    - **`checkVector` Parameters**:
      - `2` — indicates 2D points (each point has x and y).
      - `CV_32S` — specifies the data type (32-bit integers).

12. **`CV_Assert(npoints > 0);`**
    - **Purpose**: Asserts that the contour contains at least one point. If `npoints` is less than or equal to zero, the program will terminate with an error message. This ensures the validity of the contour data.

13. **`for (int j = 0; j < npoints; ++j)`**
    - **Purpose**: Iterates through each point in the current contour (`cnt`).

14. **`const bool isLastIter = j == npoints - 1;`**
    - **Purpose**: Checks if the current iteration (`j`) is the last one. If `j` is equal to `npoints - 1`, then `isLastIter` will be `true`. This helps in determining the last point of the contour.

15. **`const Point pt1 = cnt.at<Point>(j);`**
    - **Purpose**: Retrieves the current point (`j`-th point) from the contour `cnt` and stores it in a `Point` object called `pt1`.

16. **`const Point pt2 = cnt.at<Point>(isLastIter ? 0 : j + 1);`**
    - **Purpose**: Retrieves the next point in the contour to form a line segment.
    - If `isLastIter` is `true` (meaning this is the last point), it wraps around to the first point (`j + 1` would be invalid for the last point), creating a closed shape.
    - Otherwise, it retrieves the next point (`j + 1`).

17. **`cv::ThickLine(image, pt1 + offset, pt2 + offset, color_buf, thickness, lineType, 2, 0);`**
    - **Purpose**: Draws a line segment on the `image` using the `cv::ThickLine` function (likely an OpenCV function for drawing thick lines).
    - **Parameters**:
      - `image` — the image on which the contour line is being drawn.
      - `pt1 + offset` — the start point of the line segment (translated by the `offset`).
      - `pt2 + offset` — the end point of the line segment (also translated by the `offset`).
      - `color_buf` — raw color data for drawing the line.
      - `thickness` — the thickness of the line.
      - `lineType` — type of the line (e.g., 8-connected, anti-aliased, etc.).
      - `2` — shift parameter (usually for sub-pixel accuracy).
      - `0` — no additional flags.

### Summary:
This code loops through the contours and draws each one as a series of line segments connecting consecutive points. If a specific contour index (`contourIdx`) is specified, it only draws that contour. It ensures that the contours are valid, calculates the points for line segments, and draws the contours with the specified color, thickness, and line type.
</details>


### Drawing Filled Contours
```cpp
else // filled polygons
{
    int i = 0, end = (int)ncontours;
    if (contourIdx >= 0)
    {
        i = contourIdx;
        end = i + 1;
    }
    std::vector<int> indexesToFill;
    if (hierarchy.empty() || maxLevel == 0)
    {
        for (; i != end; ++i)
            indexesToFill.push_back(i);
    }
    else
    {
        std::stack<int> indexes;
        for (; i != end; ++i)
        {
            if (hierarchy.at<Vec4i>(i)[3] < 0 || contourIdx >= 0)
                indexes.push(i);
        }
        while (!indexes.empty())
        {
            const int cur = indexes.top();
            indexes.pop();

            int curLevel = -1;
            int par = cur;
            while (par >= 0)
            {
                par = hierarchy.at<Vec4i>(par)[3]; // parent
                ++curLevel;
            }
            if (curLevel <= maxLevel)
            {
                indexesToFill.push_back(cur);
            }

            int next = hierarchy.at<Vec4i>(cur)[2]; // first child
            while (next > 0)
            {
                indexes.push(next);
                next = hierarchy.at<Vec4i>(next)[0]; // next sibling
            }
        }
    }
    std::vector<Mat> contoursToFill;
    for (const int & idx : indexesToFill)
        contoursToFill.push_back(_contours.getMat(idx));
    fillPoly(image, contoursToFill, color, lineType, 0, offset);
}
```
- **`else // filled polygons`**: If `thickness` is negative, the function will fill the contour areas instead of drawing lines.
- **`indexesToFill`**: Stores the contour indices that need to be filled.
- **Hierarchy handling**: If a hierarchy is provided, the function navigates through the hierarchy tree to ensure that only contours up to `maxLevel` are filled.
    - **`std::stack<int> indexes`**: Used to traverse through the hierarchy tree.
    - **`hierarchy.at<Vec4i>(i)[3]`**: Checks for top-level contours.
    - **`par = hierarchy.at<Vec4i>(par)[3];`**: Follows parent-child relationships to determine the current level of the contour.
- **`fillPoly`**: Once all the contours to be filled are collected in `contoursToFill`, the `fillPoly` function is used to fill them with the specified color.

This function either draws contour lines or fills contour areas, depending on the `thickness` value, and handles complex contour hierarchies efficiently.

## Solution that I Found 
- i found that there was some hierarchy problems in code snippets that follows `thichkness >= 0 ` uptil else

### 1. **Create a New Branch for Your Changes**

   It’s a good practice to create a new branch for each change you work on. This keeps your work organized and isolated:
   
   ```bash
   git checkout -b fix-draw-contours-issue
   ```

### 4. **Make Your Changes**

   - Open the file where the `drawContours()` function is implemented. In OpenCV, this will likely be in `modules/imgproc/src/drawing.cpp`.
   - Apply your changes to the function to fix the contour hierarchy issue.

### 5. **Build OpenCV with Your Changes**

   - After making the changes, you need to build OpenCV locally to test it. First, create a new `build` directory and run CMake to configure the build:

     ```bash
     mkdir build
     cd build
     cmake ..
     ```

   - Then, compile OpenCV:
     ```bash
     make -j8
     ```

     This command will build OpenCV using 8 threads, speeding up the compilation process.

### 6. **Test Your Changes**

1. **Clean the Source Directory**  
   Ensure that any previous build configuration files are removed from the source directory.

   Run the following commands from the root of your OpenCV repository:
   ```bash
   rm -rf CMakeCache.txt CMakeFiles
   ```

2. **Go to the `build` Directory and Run CMake**  
   After cleaning the source directory, make sure you're in the `build` directory:
   ```bash
   cd build
   ```

3. **Run CMake Again**  
   Now that you've cleaned up, run the CMake command to configure the build:
   ```bash
   cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/Users/sankarsanbisoyi/Desktop/Python/NumpyPractice ..
   ```

Make sure the `..` at the end points to the OpenCV source directory, and the current directory is `build`.



### 2. **Commit Your Changes**

   After verifying that your changes work, it’s time to commit them:
   
   ```bash
   git add .
   git commit -m "Fix issue with drawContours hierarchy handling"
   ```

   Use a clear commit message that describes the change you’ve made.

### 8. **Push Your Changes to GitHub**

   Now that your changes are committed locally, you need to push them to your forked repository on GitHub:
   
   ```bash
   git push origin fix-draw-contours-issue
   ```

### 9. **Create a Pull Request (PR)**

   - Go to your forked repository on GitHub.
   - You should see a prompt to create a new pull request for the branch you just pushed.
   - Click **Compare & pull request**, then provide a clear title and description of your changes.
   - Link the PR to the original issue if applicable (in this case, issue #26264), and submit the PR.

### 10. **Wait for Review**

   Once your PR is submitted, OpenCV maintainers will review it. They might ask for additional changes or approve your PR if everything looks good.

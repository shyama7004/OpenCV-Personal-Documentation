Issue : [25990](https://github.com/opencv/opencv/issues/25990)

<details>
<summary>Dockerfile for reproduce_issue.cpp</summary>

```bash
FROM ubuntu:22.04

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential cmake git wget libgtk2.0-dev libavcodec-dev \
    libavformat-dev libswscale-dev libv4l-dev libatlas-base-dev \
    gfortran python3-dev python3-numpy libsuitesparse-dev \
    libeigen3-dev libhdf5-dev libprotobuf-dev protobuf-compiler \
    libgl1-mesa-glx libgl1-mesa-dev libglib2.0-dev libjpeg-dev \
    libpng-dev libtiff-dev && \
    rm -rf /var/lib/apt/lists/*

# Install Google Test
RUN apt-get update && apt-get install -y libgtest-dev && \
    cd /usr/src/gtest && \
    cmake . && \
    make && \
    cp lib/libgtest*.a /usr/lib/

# Set up OpenCV build
WORKDIR /opencv
COPY . .

RUN rm -rf build && mkdir build && cd build && \
    cmake -D CMAKE_BUILD_TYPE=RELEASE \
          -D CMAKE_INSTALL_PREFIX=/usr/local \
          -D BUILD_TESTS=ON \
          -D OPENCV_ENABLE_NONFREE=ON \
          -D BUILD_EXAMPLES=OFF \
          -D PYTHON_EXECUTABLE=$(which python3) .. && \
    make -j4 && make install && ldconfig

# Set the working directory
WORKDIR /app

# Copy the issue reproduction code
COPY reproduce_issue.cpp .

RUN g++ -o reproduce reproduce_issue.cpp \
    `pkg-config --cflags --libs opencv4` \
    -I/usr/local/include/opencv4 \
    -L/usr/local/lib \
    -lopencv_core -lopencv_imgproc

CMD ["./reproduce"]

```
</details>

<details>
  <summary>For all tests </summary>
  
  ```bash
  FROM ubuntu:22.04

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential cmake git wget libgtk2.0-dev libgtk-3-dev libavcodec-dev \
    libavformat-dev libswscale-dev libv4l-dev libatlas-base-dev \
    gfortran python3-dev python3-numpy libsuitesparse-dev \
    libeigen3-dev libhdf5-dev libprotobuf-dev protobuf-compiler \
    libgl1-mesa-glx libgl1-mesa-dev libglib2.0-dev libjpeg-dev \
    libpng-dev libtiff-dev xvfb && \
    rm -rf /var/lib/apt/lists/*

# Install Google Test
RUN apt-get update && apt-get install -y libgtest-dev && \
    cd /usr/src/gtest && \
    cmake . && \
    make && \
    cp lib/libgtest*.a /usr/lib/

# Set up OpenCV build
WORKDIR /opencv
COPY . .

RUN rm -rf build && mkdir build && cd build && \
    cmake -D CMAKE_BUILD_TYPE=RELEASE \
          -D CMAKE_INSTALL_PREFIX=/usr/local \
          -D BUILD_TESTS=ON \
          -D OPENCV_ENABLE_NONFREE=ON \
          -D BUILD_EXAMPLES=OFF \
          -D PYTHON_EXECUTABLE=$(which python3) .. && \
    make -j4 && make install && ldconfig

# Create a directory for logs in the container
RUN mkdir -p /test_logs

# Run tests and save logs (continue even if tests fail)
WORKDIR /opencv/build

# Run gapi tests
RUN ./bin/opencv_test_gapi --gtest_filter=* 2>&1 | tee /test_logs/test_gapi.log || true

# Run photo tests
RUN ./bin/opencv_test_photo --gtest_filter=* 2>&1 | tee /test_logs/test_photo.log || true

# Run Python3 tests
RUN ./bin/opencv_test_python3 --gtest_filter=* 2>&1 | tee /test_logs/test_python3.log || true

# Run imgproc tests
RUN ./bin/opencv_test_imgproc --gtest_filter=* 2>&1 | tee /test_logs/test_imgproc.log || true

# Run videoio tests
RUN ./bin/opencv_test_videoio --gtest_filter=* 2>&1 | tee /test_logs/test_videoio.log || true

# Run gapi performance tests
RUN ./bin/opencv_perf_gapi --gtest_filter=* 2>&1 | tee /test_logs/perf_gapi.log || true

# Run imgproc performance tests
RUN ./bin/opencv_perf_imgproc --gtest_filter=* 2>&1 | tee /test_logs/perf_imgproc.log || true

# Print the logs to the console
RUN cat /test_logs/test_gapi.log
RUN cat /test_logs/test_photo.log
RUN cat /test_logs/test_python3.log
RUN cat /test_logs/test_imgproc.log
RUN cat /test_logs/test_videoio.log
RUN cat /test_logs/perf_gapi.log
RUN cat /test_logs/perf_imgproc.log
```
</details>

<details>
<summary>For testing only pyramid tests</summary>

```bash
FROM ubuntu:22.04

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential cmake git wget libgtk2.0-dev libavcodec-dev \
    libavformat-dev libswscale-dev libv4l-dev libatlas-base-dev \
    gfortran python3-dev python3-numpy libsuitesparse-dev \
    libeigen3-dev libhdf5-dev libprotobuf-dev protobuf-compiler \
    libgl1-mesa-glx libgl1-mesa-dev libglib2.0-dev libjpeg-dev \
    libpng-dev libtiff-dev && \
    rm -rf /var/lib/apt/lists/*

# Install Google Test
RUN apt-get update && apt-get install -y libgtest-dev && \
    cd /usr/src/gtest && \
    cmake . && \
    make && \
    cp lib/libgtest*.a /usr/lib/

# Set up OpenCV build
WORKDIR /opencv
COPY . .

RUN rm -rf build && mkdir build && cd build && \
    cmake -D CMAKE_BUILD_TYPE=RELEASE \
          -D CMAKE_INSTALL_PREFIX=/usr/local \
          -D BUILD_TESTS=ON \
          -D OPENCV_ENABLE_NONFREE=ON \
          -D BUILD_EXAMPLES=OFF \
          -D PYTHON_EXECUTABLE=$(which python3) .. && \
    make -j4 && make install && ldconfig

# Create a directory for logs in the container
RUN mkdir -p /test_logs

# Check for the existence of the test binary
RUN ls -la /opencv/build/bin/

# Run only pyramid-related tests in the imgproc module and save the logs
WORKDIR /opencv/build
RUN ./bin/opencv_test_imgproc --gtest_filter=*PyrUp*:*PyrDown* 2>&1 | tee /test_logs/pyramid_tests.log

# Print the log file to the console
RUN cat /test_logs/pyramid_tests.log

```
</details>

<details>
<summary>Fix - 1</summary>

```cpp
void cv::pyrDown(InputArray _src, OutputArray _dst, const Size& _dsz, int borderType)
{
    CV_INSTRUMENT_REGION();

    CV_Assert(borderType != BORDER_CONSTANT);

    CV_OCL_RUN(_src.dims() <= 2 && _dst.isUMat(),
               ocl_pyrDown(_src, _dst, _dsz, borderType))

    CV_OVX_RUN(_src.dims() <= 2,
               openvx_pyrDown(_src, _dst, _dsz, borderType))

    Mat src = _src.getMat();
    Size dsz = _dsz.empty() ? Size(src.cols/2, src.rows/2) : _dsz;
    _dst.create(dsz, src.type());
    Mat dst = _dst.getMat();
    int depth = src.depth();

    if (src.isSubmatrix() && !(borderType & BORDER_ISOLATED))
    {
        Point ofs;
        Size wsz(src.cols, src.rows);
        src.locateROI(wsz, ofs);
        
        Size parentSize = wsz;
        Point offset = ofs;
        
        Size parentDsz((parentSize.width + 1)/2, (parentSize.height + 1)/2);
        Point offsetDsz(offset.x/2, offset.y/2);
        
        CALL_HAL(pyrDown, cv_hal_pyrdown_offset, src.data, src.step, src.cols, src.rows,
                 dst.data, dst.step, dst.cols, dst.rows, depth, src.channels(),
                 offsetDsz.x, offsetDsz.y, 
                 parentDsz.width - dst.cols - offsetDsz.x,
                 parentDsz.height - dst.rows - offsetDsz.y,
                 borderType & (~BORDER_ISOLATED));
    }
    else
    {
        CALL_HAL(pyrDown, cv_hal_pyrdown, src.data, src.step, src.cols, src.rows, 
                dst.data, dst.step, dst.cols, dst.rows, depth, src.channels(), borderType);
    }

    PyrFunc func = 0;
    if (depth == CV_8U)
        func = pyrDown_< FixPtCast<uchar, 8> >;
    else if (depth == CV_16S)
        func = pyrDown_< FixPtCast<short, 8> >;
    else if (depth == CV_16U)
        func = pyrDown_< FixPtCast<ushort, 8> >;
    else if (depth == CV_32F)
        func = pyrDown_< FltCast<float, 8> >;
    else if (depth == CV_64F)
        func = pyrDown_< FltCast<double, 8> >;
    else
        CV_Error(cv::Error::StsUnsupportedFormat, "");

    func(src, dst, borderType);
}
```
</details>


### The key changes I've made to fix the inconsistency issue are:

1. Changed the default destination size calculation:

- Original -> Size((src.cols + 1)/2, (src.rows + 1)/2)
- New -> Size(src.cols/2, src.rows/2)

This removes the `1`that was causing size inconsistencies between ROI and full image processing, ROI handling is fixed according to this :).

<details>
  <summary>Test-1 fix </summary>

  ```cpp
TEST(Imgproc_PyrDown, pyrDown_regression_custom)
{

    cv::Mat src(16, 16, CV_16UC3, cv::Scalar(0, 0, 0));
    int swidth = src.cols;
    int sheight = src.rows;
    int cn = src.channels();
    int count = 0;

    for (int y = 0; y < sheight; y++) {
        ushort *src_c = src.ptr<ushort>(y);
        for (int x = 0; x < swidth * cn; x++) {
            src_c[x] = (count++) % 10;
        }
    }

    cv::Mat dst_full(swidth / 2 + 1, sheight / 2 + 1, CV_16UC3, cv::Scalar(0, 0, 0));
    pyrDown(src, dst_full, cv::Size(dst_full.cols, dst_full.rows));

    cv::Mat src_roi = src(cv::Rect(0, 0, 14, 14));
    cv::Mat dst_roi(src_roi.cols / 2 + 1, src_roi.rows / 2 + 1, CV_16UC3, cv::Scalar(0, 0, 0));
    pyrDown(src_roi, dst_roi, cv::Size(dst_roi.cols, dst_roi.rows));

    cv::Mat diff = dst_full(cv::Rect(0, 0, dst_roi.cols, dst_roi.rows)) - dst_roi;
    double max_diff;
    cv::minMaxLoc(diff, nullptr, &max_diff);

    ASSERT_EQ(max_diff, 0) << "pyrDown result differs between full image and ROI processing";
}
```
  </details>

  
<details>
  <summary>Test-2 (chat gpt enhanced) </summary>
  
  ```cpp
  TEST(Imgproc_PyrDown, roi_consistency_with_borders)
{
    // Create source matrix 16x16 with 3 channels (16-bit unsigned)
    Mat src(16, 16, CV_16UC3, Scalar(0, 0, 0));

    // Fill source matrix with test pattern
    {
        int swidth = src.cols;
        int sheight = src.rows;
        int cn = src.channels();
        int count = 0;
        for (int y = 0; y < sheight; y++)
        {
            ushort* src_c = src.ptr<ushort>(y);
            for (int x = 0; x < swidth * cn; x++)
            {
                src_c[x] = (count++) % 10;
            }
        }
    }

    // Test cases with different border types
    int borderTypes[] = {BORDER_DEFAULT, BORDER_REPLICATE};

    for (int i = 0; i < 2; i++)
    {
        int borderType = borderTypes[i];

        // Test 1: Full image pyrDown
        Mat dst((src.cols + 1)/2, (src.rows + 1)/2, CV_16UC3);
        pyrDown(src, dst, dst.size(), borderType);

        // Test 2: ROI pyrDown
        Mat srcp = src(Rect(2, 2, 12, 12));  // Internal ROI
        Mat dstp((srcp.cols + 1)/2, (srcp.rows + 1)/2, CV_16UC3);
        pyrDown(srcp, dstp, dstp.size(), borderType);

        // Get corresponding region from full image result
        Mat dstroi = dst(Rect(1, 1, dstp.cols, dstp.rows));

        // Compare results
        Mat diff = dstroi - dstp;
        EXPECT_EQ(countNonZero(diff), 0)
            << "pyrDown is not consistent between ROI and full image processing with border type "
            << borderType << std::endl
            << "Difference matrix:" << std::endl << diff;
    }
}
```
</details>
<details>
  <summary> Improved the previously used test</summary>

  ```cpp
TEST(Imgproc_PyrDown, pyrDown_regression_custom)
{
    Mat src(16, 16, CV_16UC3, Scalar(0, 0, 0));
    int swidth = src.cols;
    int sheight = src.rows;
    int cn = src.channels();
    int count = 0;

    for (int y = 0; y < sheight; y++)
    {
        ushort* src_c = src.ptr<ushort>(y);
        for (int x = 0; x < swidth * cn; x++)
        {
            src_c[x] = (count++) % 10;
        }
    }

    Size dstSize((src.cols + 1) / 2, (src.rows + 1) / 2);
    Mat dst(dstSize, CV_16UC3, Scalar(0, 0, 0));
    pyrDown(src, dst, dstSize);

    Mat srcp = src(Rect(0, 0, 14, 14));
    Size dstpSize((srcp.cols + 1) / 2, (srcp.rows + 1) / 2);
    Mat dstp(dstpSize, CV_16UC3, Scalar(0, 0, 0));
    pyrDown(srcp, dstp, dstpSize);

    int borderOffset = 1;
    Mat dst_center = dst(Rect(borderOffset, borderOffset, dst.cols - 2 * borderOffset, dst.rows - 2 * borderOffset));
    Mat dstp_center = dstp(Rect(borderOffset, borderOffset, dstp.cols - 2 * borderOffset, dstp.rows - 2 * borderOffset));

    Mat diff = dst_center - dstp_center;
    EXPECT_EQ(cv::countNonZero(diff.reshape(1)), 0) << "The central difference between dst and dstp is not zero.";
}

```
</details>
<details>
<summary>Chat-Gpt fix </summary>

```cpp
void cv::pyrDown(InputArray _src, OutputArray _dst, const Size& _dsz, int borderType)
{
    CV_INSTRUMENT_REGION();

    CV_Assert(borderType != BORDER_CONSTANT);

    CV_OCL_RUN(_src.dims() <= 2 && _dst.isUMat(),
               ocl_pyrDown(_src, _dst, _dsz, borderType))

    CV_OVX_RUN(_src.dims() <= 2,
               openvx_pyrDown(_src, _dst, _dsz, borderType))

    Mat src = _src.getMat();
    Size dsz = _dsz.empty() ? Size(src.cols / 2, src.rows / 2) : _dsz;
    _dst.create(dsz, src.type());
    Mat dst = _dst.getMat();
    int depth = src.depth();

    // Check if source is a submatrix and apply BORDER_ISOLATED if needed
    if (src.isSubmatrix() && !(borderType & BORDER_ISOLATED))
    {
        Point ofs;
        Size wsz(src.cols, src.rows);
        src.locateROI(wsz, ofs);

        Size parentSize = wsz;
        Point offset = ofs;

        Size parentDsz((parentSize.width + 1) / 2, (parentSize.height + 1) / 2);
        Point offsetDsz(offset.x / 2, offset.y / 2);

        // Call HAL function with BORDER_ISOLATED added to borderType
        CALL_HAL(pyrDown, cv_hal_pyrdown_offset, src.data, src.step, src.cols, src.rows,
                 dst.data, dst.step, dst.cols, dst.rows, depth, src.channels(),
                 offsetDsz.x, offsetDsz.y,
                 parentDsz.width - dst.cols - offsetDsz.x,
                 parentDsz.height - dst.rows - offsetDsz.y,
                 borderType | BORDER_ISOLATED);
    }
    else
    {
        // Apply BORDER_ISOLATED to the normal pyrDown call as well
        CALL_HAL(pyrDown, cv_hal_pyrdown, src.data, src.step, src.cols, src.rows, 
                dst.data, dst.step, dst.cols, dst.rows, depth, src.channels(), borderType | BORDER_ISOLATED);
    }

    PyrFunc func = 0;
    if (depth == CV_8U)
        func = pyrDown_< FixPtCast<uchar, 8> >;
    else if (depth == CV_16S)
        func = pyrDown_< FixPtCast<short, 8> >;
    else if (depth == CV_16U)
        func = pyrDown_< FixPtCast<ushort, 8> >;
    else if (depth == CV_32F)
        func = pyrDown_< FltCast<float, 8> >;
    else if (depth == CV_64F)
        func = pyrDown_< FltCast<double, 8> >;
    else
        CV_Error(cv::Error::StsUnsupportedFormat, "");

    // Apply the function with BORDER_ISOLATED for consistent behavior across ROIs
    func(src, dst, borderType | BORDER_ISOLATED);
}
```
</details>
<details>
<summary>Fix-3</summary>

```cpp
void cv::pyrDown( InputArray src, OutputArray dst, const Size& _dsz, int borderType )
{
    CV_INSTRUMENT_REGION();

    CV_Assert(borderType != BORDER_CONSTANT);

    CV_OCL_RUN(_src.dims() <= 2 && _dst.isUMat(),
               ocl_pyrDown(_src, dst, dsz, borderType))

    CV_OVX_RUN(_src.dims() <= 2,
               openvx_pyrDown(_src, dst, dsz, borderType))

    Mat src = _src.getMat();
    Size dsz = dsz.empty() ? Size((src.cols + 1) / 2, (src.rows + 1) / 2) : dsz;
    _dst.create(dsz, src.type());
    Mat dst = _dst.getMat();
    int depth = src.depth();

    if (src.isSubmatrix() && !(borderType & BORDER_ISOLATED))
    {
        Point ofs;
        Size wsz(src.cols, src.rows);
        src.locateROI(wsz, ofs);
        CALL_HAL(pyrDown, cv_hal_pyrdown_offset, src.data, src.step, src.cols, src.rows,
                 dst.data, dst.step, dst.cols, dst.rows, depth, src.channels(),
                 ofs.x, ofs.y, wsz.width - src.cols - ofs.x, wsz.height - src.rows - ofs.y, borderType & (~BORDER_ISOLATED));
    }
    else
    {
        CALL_HAL(pyrDown, cv_hal_pyrdown, src.data, src.step, src.cols, src.rows,
                 dst.data, dst.step, dst.cols, dst.rows, depth, src.channels(), borderType);
    }

    PyrFunc func = 0;
    if (depth == CV_8U)
        func = pyrDown_<FixPtCast<uchar, 8>>;
    else if (depth == CV_16S)
        func = pyrDown_<FixPtCast<short, 8>>;
    else if (depth == CV_16U)
        func = pyrDown_<FixPtCast<ushort, 8>>;
    else if (depth == CV_32F)
        func = pyrDown_<FltCast<float, 8>>;
    else if (depth == CV_64F)
        func = pyrDown_<FltCast<double, 8>>;
    else
        CV_Error(cv::Error::StsUnsupportedFormat, "");

    func(src, dst, borderType);
}
```
</details>

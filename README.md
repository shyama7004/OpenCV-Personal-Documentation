# OpenCV Personal Documentation

Welcome to my personal documentation for OpenCV! This repository is a collection of my notes, tutorials, and projects related to OpenCV, a powerful open-source library for computer vision and image processing. This documentation aims to help me (and others) understand and utilize OpenCV effectively.

## 📋 Table of Contents

- [Introduction](#introduction)
- [OpenCV Tutorials](#opencv-tutorials)
- [NumPy Tutorials](#numpy-tutorials)
- [Core OpenCV Modules](#core-opencv-modules)
- [OpenCV.js Tutorials](#opencvjs-tutorials)
- [Tutorials for Contrib Modules](#tutorials-for-contrib-modules)
- [Projects and Examples](#projects-and-examples)
- [Community and Contributions](#community-and-contributions)
- [Contact](#contact)

## 📖 Introduction

Welcome to my OpenCV documentation repository! Here you'll find a comprehensive collection of tutorials, projects, and notes aimed at mastering OpenCV for computer vision and image processing.

## 🖥️ OpenCV Tutorials

### Core Learning (Month 1)
1. **Introduction to OpenCV**
   - [Overview of OpenCV](https://opencv.org/about/) 
   - [Installation and Setup](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Main-Concepts/Quick%20Start.md)
   - [Basic Structures (Mat, Scalar, Point, etc.)](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Main-Concepts/Basic%20Structures.md)
   - **Revision Tips**: Practice by writing small programs to load, display, and manipulate images.

2. **Core Operations**
   - [Basic Operations on Images](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Main-Concepts/Basic%20Operations%20on%20Images.md)
   - [Pixel Manipulation](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Main-Concepts/Changing%20Colorspaces.md)
   - [Arithmetic Operations](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Main-Concepts/Arithmetic%20Operations.mdl)
|
   - [Logic Operations](https://docs.opencv.org/master/d0/d86/tutorial_py_image_arithmetics.html#image-blending-using-pyramids)
   - [Geometric Transformations](https://docs.opencv.org/master/da/d6e/tutorial_py_geometric_transformations.html)
   - **Revision Tips**: Implement small projects like image filters, blending, and basic transformations.

### Advanced Topics and Projects (Month 2)
3. **Image Processing**
   - [Smoothing and Blurring](https://docs.opencv.org/master/d4/d13/tutorial_py_filtering.html)
   - [Image Thresholding](https://docs.opencv.org/master/d7/d4d/tutorial_py_thresholding.html)
   - [Morphological Operations](https://docs.opencv.org/master/d9/d61/tutorial_py_morphological_ops.html)
   - [Canny Edge Detection](https://docs.opencv.org/master/da/d22/tutorial_py_canny.html)
   - [Image Gradients](https://docs.opencv.org/master/dd/d43/tutorial_py_gradients.html)
   - **Advanced Image Processing Techniques**:

#### Restoration
1. **Wiener Filtering**:
   - [Wiener Filtering with SciPy](https://scipy-cookbook.readthedocs.io/items/SignalSmooth.html)

2. **Richardson-Lucy Deconvolution**:
   - [Richardson-Lucy Deconvolution (scikit-image)](https://scikit-image.org/docs/stable/auto_examples/filters/plot_deconvolution.html)

3. **Total Variation Denoising**:
   - [Total Variation Denoising (scikit-image)](https://scikit-image.org/docs/stable/auto_examples/filters/plot_denoise.html)

#### Denoising
1. [Gaussian Blurring (OpenCV)](https://docs.opencv.org/master/dc/dd3/tutorial_gausian_median_blur_bilateral_filter.html)
2. [Median Blurring (OpenCV)](https://docs.opencv.org/master/dc/dd3/tutorial_gausian_median_blur_bilateral_filter.html)
3. [Non-Local Means Denoising (OpenCV)](https://docs.opencv.org/master/d5/d69/tutorial_py_non_local_means.html)

#### Segmentation
1. [Image Thresholding (OpenCV)](https://docs.opencv.org/master/d7/d4d/tutorial_py_thresholding.html)
2. [Canny Edge Detection (OpenCV)](https://docs.opencv.org/master/da/d22/tutorial_py_canny.html)
3. [Watershed Algorithm (OpenCV)](https://docs.opencv.org/master/d3/db4/tutorial_py_watershed.html)
4. [Graph-Based Segmentation (PyTorch Geometric)](https://pytorch-geometric.readthedocs.io/en/latest/notes/segmentation.html)
   - **Revision Tips**: Create an image processing pipeline that includes smoothing, thresholding, edge detection, and advanced techniques.

4. **Feature Detection and Description**
   - [Feature Detection and Description](https://docs.opencv.org/master/dc/dc3/tutorial_py_matcher.html)
   - [Feature Matching](https://docs.opencv.org/master/dc/dc3/tutorial_py_matcher.html)
   - [Cascade Classifiers](https://docs.opencv.org/master/d7/d8b/tutorial_py_face_detection.html)
   - **Revision Tips**: Develop a simple application for detecting and matching features in images.

5. **Video Analysis**
   - [Video Capture](https://docs.opencv.org/master/dd/d43/tutorial_py_video_display.html)
   - [Background Subtraction](https://docs.opencv.org/master/d1/dc5/tutorial_background_subtraction.html)
   - [Motion Detection](https://docs.opencv.org/master/df/d9d/tutorial_py_colorspaces.html)
   - [Object Tracking](https://docs.opencv.org/master/d7/d00/tutorial_meanshift.html)
   - **Revision Tips**: Create a program that captures video and implements background subtraction and object tracking.

### Specialization and Contributions (Month 3)
6. **Core Computer Vision Concepts**
   - [Camera Calibration with OpenCV](https://docs.opencv.org/4.x/dc/dbb/tutorial_py_calibration.html)
   - [Depth Map from Stereo Images](https://docs.opencv.org/4.x/dd/d53/tutorial_py_depthmap.html)
   - [3D Reconstruction from Stereo Images](https://docs.opencv.org/4.x/da/de9/tutorial_py_epipolar_geometry.html)

7. **Machine Learning with OpenCV**
   - [KNN](https://docs.opencv.org/master/d5/d26/tutorial_py_knn_understanding.html)
   - [SVM](https://docs.opencv.org/master/d1/d73/tutorial_introduction_to_svm.html)
   - [Decision Trees](https://docs.opencv.org/master/d3/dc0/group__ml.html)
   - [K-means Clustering](https://docs.opencv.org/master/d1/d5c/tutorial_py_kmeans_opencv.html)
   - **Hyperparameter Tuning**: [Hyperparameter Tuning Techniques](https://machinelearningmastery.com/hyperparameter-optimization-with-random-search-and-grid-search/)
   - **Model Evaluation**: [Metrics and Methods for Model Evaluation](https://scikit-learn.org/stable/modules/model_evaluation.html)
   - **Deployment**: [Strategies for Deploying Machine Learning Models](https://www.tensorflow.org/tfx/guide/serving)
   - **Revision Tips**: Implement machine learning models to classify images or perform clustering.

8. **Deep Learning with OpenCV**
   - [Deep Learning with OpenCV](https://docs.opencv.org/master/d5/de7/tutorial_dnn_googlenet.html)
   - [Training Models](https://docs.opencv.org/master/d2/dc0/tutorial_introduction_to_traincascade.html)
   - [Image Classification](https://docs.opencv.org/master/d5/de7/tutorial_dnn_googlenet.html)
   - [YOLO, SSD](https://docs.opencv.org/master/d5/de7/tutorial_dnn_googlenet.html)
   - [Semantic Segmentation](https://docs.opencv.org/master/da/d60/tutorial_bounding_boxes.html)
   - **Revision Tips**: Develop applications that use pre-trained models for image classification and object detection.

### Additional Focus Areas (Throughout the Preparation)
9. **OpenCV Contrib Modules**
   - [Extended Image Processing (ximgproc)](https://docs.opencv.org/4.x/d9/dc5/tutorial_ximgproc_intro.html)
   - [Advanced Calibration Techniques and 3D Data Handling (calib3d)](https://docs.opencv.org/4.x/d9/d0c/group__calib3d.html)
   - [2D Features Framework (features2d)](https://docs.opencv.org/4.x/db/d27/tutorial_py_table_of_contents_features2d.html)

10. **Computational Photography**
   - [HDR Imaging with OpenCV](https://docs.opencv.org/4.x/d3/db7/tutorial_hdr_imaging.html)
   - [Image Stitching with OpenCV](https://docs.opencv.org/4.x/d8/d19/tutorial_stitcher.html)
   - [Deblurring Techniques](https://www.pyimagesearch.com/2021/08/16/opencv-deblurring-images-with-fft-and-inverse-fft/)

11. **Real-Time Applications**
   - [Optimizing with Multi-Threading and GPU Acceleration](https://docs.opencv.org/4.x/d2/d0a/tutorial_introduction_to_opencl.html)
   - [Real-Time Video Processing and Augmented Reality](https://www.pyimagesearch.com/2021/03/15/augmented-reality-with-aruco-markers-and-opencv/)

12. **Documentation and Testing**
    - **Writing Documentation**: [Writing Documentation](https://opencv.org/documentation/)
    - **Contributing to the Tests**: [OpenCV Tests](https://github.com/opencv/opencv/tree/master/modules/ts)
    - **Understanding the Build System**: [CMake Guide](https://docs.opencv.org/master/db/d05/tutorial_config_reference.html)
    - **Revision Tips**: Regularly review and update your contributions, and ensure your changes pass all tests.

13. **Contributing to OpenCV**
    - **Understanding the Codebase**: [OpenCV Codebase](https://github.com/opencv/opencv)
    - **Writing Good Commit Messages**: [Commit Message Guidelines](https://chris.beams.io/posts/git-commit/)
    - **Submitting Pull Requests**: [Pull Request Guide](https://opensource.com/article/19/7/create-pull-request-github)
    - **Code Reviews and Guidelines**: [Coding Guidelines](https://github.com/opencv/opencv/wiki/Coding_Style_Guide)
    - **Revision Tips**: Actively participate in code reviews and regularly contribute to the repository.

### Extra Topics (If Time Permits)

14. **Histograms**
    - **Understanding Histograms**: [Introduction to Histograms](https://docs.opencv.org/master/d1/db7/tutorial_py_histogram_begins.html)
    - **Histogram Calculation**: [Calculating Histograms](https://docs.opencv.org/master/d1/db7/tutorial_py_histogram_begins.html)
    - **Histogram Equalization**: [Equalizing Histograms](https://docs.opencv.org/master/d5/daf/tutorial_py_histogram_equalization.html)
    - **Histogram Comparison**: [Comparing Histograms](https://docs.opencv.org/master/dc/d8b/tutorial_py_histogram_comparison.html)
    - **Revision Tips**: Use histograms for image analysis and enhancement, such as contrast adjustment.

15. **Contours**
    - **Finding Contours**: [Finding Contours](https://docs.opencv.org/master/df/d0d/tutorial_find_contours.html)
    - **Contour Features**: [Contour Features](https://docs.opencv.org/master/d1/d32/tutorial_py_contour_properties.html)
    - **Contour Hierarchy**: [Contour Hierarchy](https://docs.opencv.org/master/d9/d8b/tutorial_py_contours_hierarchy.html)
    - **Shape Matching**: [Shape Matching](https://docs.opencv.org/master/d1/d85/tutorial_py_contour_features.html)
    - **Revision Tips**: Develop applications for detecting and analyzing shapes in images.

16. **Color Spaces**
    - **Color Space Conversion**: [Color Spaces](https://docs.opencv.org/master/df/d9d/tutorial_py_colorspaces.html)
    - **Object Tracking with Color**: [Object Tracking](https://docs.opencv.org/master/df/d9d/tutorial_py_colorspaces.html)
    - **Histogram Backprojection**: [Backprojection](https://docs.opencv.org/master/dd/d11/tutorial_py_histogram_backprojection.html)
    - **Revision Tips**: Implement color-based object tracking and image segmentation.

## 📚 NumPy Tutorials
### Basic NumPy
1. **Introduction to NumPy**:
   - Overview of NumPy and its uses.
   - [NumPy Basics](https://numpy.org/doc/stable/user/quickstart.html)

2. **Array Creation and Manipulation**:
   - Creating arrays (from lists, using functions like `arange`, `linspace`).
   - Reshaping arrays.
   - [Creating Arrays](https://numpy.org/doc/stable/user/basics.creation.html)

3. **Indexing and Slicing**:
   - Basic slicing and indexing.
   - Advanced indexing.
   - [Indexing and Slicing](https://numpy.org/doc/stable/user/basics.indexing.html)

4. **Array Operations**:
   - Arithmetic operations (addition, subtraction, multiplication, division).
   - Basic linear algebra operations.
   - [Array Operations](https://numpy.org/doc/stable/reference/routines.math.html)

5. **Array Attributes**:
   - Shape, size, and dimensions.
   - Data types and type conversion.
   - [Array Attributes](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html)

6. **Basic Linear Algebra**:
   - Dot product and matrix multiplication.
   - Transpose of a matrix.
   - [Linear Algebra](https://numpy.org/doc/stable/reference/routines.linalg.html)

### Advanced NumPy
1. **Broadcasting**:
   - Rules of broadcasting.
   - Practical examples of broadcasting.
   - [Broadcasting](https://numpy.org/doc/stable/user/basics.broadcasting.html)

2. **Vectorized Operations**:
   - Element-wise operations.
   - Using universal functions (ufuncs).
   - [Vectorized Operations](https://numpy.org/doc/stable/reference/ufuncs.html)

3. **Advanced Linear Algebra**:
   - Eigenvalues and eigenvectors.
   - Singular Value Decomposition (SVD).
   - Inverse of a matrix.
   - [Advanced Linear Algebra](https://numpy.org/doc/stable/reference/routines.linalg.html)

4. **Random Sampling**:
   - Generating random numbers.
   - Sampling from different distributions.
   - [Random Sampling](https://numpy.org/doc/stable/reference/random/index.html)

5. **Fancy Indexing**:
   - Using boolean arrays for indexing.
   - Index arrays and multidimensional indexing.
   - [Fancy Indexing](https://numpy.org/doc/stable/reference/arrays.indexing.html)

6. **Sorting and Searching**:
   - Sorting arrays.
   - Searching and filtering arrays.
   - [Sorting and Searching](https://numpy.org/doc/stable/reference/routines.sort.html)

7. **Data Manipulation**:
   - Stacking and splitting arrays.
   - Joining and concatenating arrays.
   - [Data Manipulation](https://numpy.org/doc/stable/user/basics.joining.html)

### Revision Tips
- **Practice Problems**: Solve problems on platforms like [NumPy Exercises](https://github.com/rougier/numpy-100).
- **Implement Projects**: Build small projects that require image processing with OpenCV and NumPy.
- **Review and Repeat**: Regularly revisit topics and solve related problems.
- **Use Flashcards**: Create flashcards for important functions and methods.
- **Engage in Discussions**: Participate in forums or study groups to discuss concepts and applications.

<!--### Additional Resources
- **Official NumPy Documentation**: [NumPy Documentation](https://numpy.org/doc/stable/)
- **Tutorials and Guides**:
  - [NumPy Quickstart Tutorial](https://numpy.org/doc/stable/user/quickstart.html)
  - [Real Python NumPy Tutorial](https://realpython.com/numpy-tutorial/)
  - [W3Schools NumPy Tutorial](https://www.w3schools.com/python/numpy_intro.asp)
- **Online Courses**:
  - [Coursera: Introduction to Data Science in Python](https://www.coursera.org/learn/python-data-analysis)
  - [Udacity: Data Analyst Nanodegree](https://www.udacity.com/course/data-analyst-nanodegree--nd002)
-->


## 📦 Core OpenCV Modules

- **Core Functionality**: [Core Functionality](https://docs.opencv.org/master/d0/d0a/classcv_1_1Mat.html)
- **Image Processing**: [Image Processing](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)
- **Video Analysis**: [Video Analysis](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)
- **Camera Calibration and 3D Reconstruction**: [Camera Calibration](https://docs.opencv.org/master/dc/dbb/tutorial_py_calibration.html)
- **Object Detection**: [Object Detection](https://docs.opencv.org/master/d7/d8b/tutorial_py_face_detection.html)
- **Machine Learning**: [Machine Learning](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)
- **Deep Learning**: [Deep Learning](https://docs.opencv.org/master/d5/de7/tutorial_dnn_googlenet.html)

## 🌐 OpenCV.js Tutorials

- **Introduction to OpenCV.js**: [OpenCV.js Introduction](https://docs.opencv.org/master/d5/d10/tutorial_js_root.html)
- **Setup and Installation**: [Setup Guide](https://docs.opencv.org/master/d5/d10/tutorial_js_root.html)
- **Basic Image Processing with OpenCV.js**: [Image Processing](https://docs.opencv.org/master/d5/d10/tutorial_js_root.html)
- **Video Processing with OpenCV.js**: [Video Processing](https://docs.opencv.org/master/d5/d10/tutorial_js_root.html)
- **Object Detection with OpenCV.js**: [Object Detection](https://docs.opencv.org/master/d5/d10/tutorial_js_root.html)

## 📚 Tutorials for Contrib Modules

- **Face Recognition**: [Face Recognition](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)
- **Text Detection and Recognition**: [Text Detection](https://docs.opencv.org/master/d5/d10/tutorial_js_root.html)
- **Optical Flow**: [Optical Flow](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)
- **Tracking**: [Tracking](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)

## 💡 Projects and Examples

- **Image Filters and Effects**: [Image Filters](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)
- **Object Detection and Tracking**: [Object Detection](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)
- **Face Recognition System**: [Face Recognition](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)
- **Augmented Reality Applications**: [AR Applications](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)
- **Real-Time Video Processing**: [Video Processing](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)

## 🌍 Community and Contributions

- **How to Contribute**: [Contribution Guide](https://docs.opencv.org/master/d3/dc0/tutorial_py_haar_cascade.html)
- **OpenCV GitHub Repository**: [OpenCV GitHub](https://github.com/opencv/opencv)
- **Discussion Forum**: [OpenCV Forum](https://forum.opencv.org/)
- **GSoC and Other Opportunities**: [GSoC Guide](https://opencv.org/gsoc/)

## 📞 Contact

For any questions or feedback, feel free to reach out!

- **GitHub**: [My GitHub Profile](https://github.com/shyama7004)
- **Email**: sujatabisoyi4@gmail.com
---
<!--
## Old practice

- ### Introduction to OpenCV
  - #### Other Platforms
    - [Installation in MacOS (Day-1)](https://github.com/shyama7004/OpenCV-Personal-Documentation-MacOS-/blob/main/readme.md/Installation.md)
  - #### Usage Basics
    - [Getting Started with Images (Day-2)](https://github.com/shyama7004/OpenCV-Personal-Documentation-MacOS-/blob/main/readme.md/Usage%20basics.md)
    - [Installing OpenCV and Configuring VS Code](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/readme.md/Other%20Defs/Installing%20OpenCV%20and%20Configuring%20VS%20Code.md)
  - #### Miscellaneous
    - [Writing Documentation for OpenCV (Day-2)](https://github.com/shyama7004/OpenCV-Personal-Documentation-MacOS-/blob/main/readme.md/Writing%20documentation%20for%20OpenCV.md)
    - [Doxygen (Day-3)](https://github.com/shyama7004/OpenCV-Personal-Documentation-MacOS-/tree/main/readme.md/Doxygen.md)

## 📊 NumPy Tutorials

- [NumPy Overview (Day-4)](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/readme.md/Numpy.md)
  - [What is NumPy? (Day-4)](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/readme.md/What%20is%20NumPy%3F.md)
  - [Installation (Day-4)](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/readme.md/Other%20Defs/Numpy_Installation.md)
  - [NumPy Quickstart (Day-5)](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/readme.md/Other%20Defs/NumPy%20quickstart.md)
  - [NumPy: The Absolute Basics for Beginners (Day-6)](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/readme.md/Numpy.md)
- ### NumPy Fundamentals
  - [Array Creation (Day-7)](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/readme.md/NumPy%20fundamentals/Array%20creation.md)
  - [Indexing on ndarrays (Day-8)](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/readme.md/Other%20Defs/Indexing%20on%20ndarrays.md)
  -->
  

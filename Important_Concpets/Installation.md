## Installation Guide

### Overview

This guide helps you install and build OpenCV on macOS, specifically version 4.10.0. It assumes you have basic development tools and Python installed on your system.

### Required Packages

- **CMake 3.9 or higher**
- **Git**
- **Python 2.7 or later and Numpy 1.5 or later**

### Preliminary Steps

1. **Xcode License Agreement**:
   Ensure you have agreed to the Xcode license by running:

   ```bash
   sudo xcodebuild -license
   ```

   This step is essential as Xcode tools require this agreement.

2. **Install Xcode Command Line Tools**:
   If not already installed, run:
   ```bash
   xcode-select --install
   ```
   These tools are necessary for compiling code and other development tasks.

### Installing CMake

1. **Download CMake**:

   - Go to the [CMake download page](https://cmake.org/download/).
   - Download and install the `.dmg` package for macOS.

2. **Set Up CMake for Command Line Use**:

   - Open the CMake application from your Applications folder.
   - In the CMake app, navigate to `Tools` > `How to Install For Command Line Use`.
   - Follow the instructions to create command line links.

3. **Verify Installation**:

   - Run `cmake --version` to ensure CMake is installed correctly.

   Alternatively, you can install CMake using Homebrew:

   ```bash
   brew install cmake
   ```

### Getting OpenCV Source Code

1. **Stable Version**:

   - Visit the OpenCV downloads page and download the source archive.
   - Unpack the archive.

2. **Cutting-edge Version**:
   - Use Git to clone the latest OpenCV repository:
     ```bash
     cd ~/<my_working_directory>
     git clone https://github.com/opencv/opencv.git
     git clone https://github.com/opencv/opencv_contrib.git
     ```

### Building OpenCV from Source Using CMake

1. **Create a Build Directory**:

   - Create a temporary directory to store the build files:
     ```bash
     mkdir build_opencv
     cd build_opencv
     ```

2. **Configuring the Build**:

   - Run CMake with optional parameters:
     ```bash
     cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_EXAMPLES=ON ../opencv
     ```
   - Alternatively, use the CMake GUI:
     - Set the OpenCV source path.
     - Set the build path.
     - Configure and generate the build files.

3. **Common CMake Parameters**:

   - `CMAKE_BUILD_TYPE=Release` or `Debug` (specify build type).
   - `OPENCV_EXTRA_MODULES_PATH` (set path to extra modules from `opencv_contrib`).
   - `BUILD_DOCS=ON` (build documentation if Doxygen is installed).
   - `BUILD_EXAMPLES=ON` (build example programs).

4. **Optional: Building Python Bindings**:

   - Set Python parameters for Python 3:
     ```bash
     PYTHON3_EXECUTABLE=<path to python>
     PYTHON3_INCLUDE_DIR=/usr/include/python<version>
     PYTHON3_NUMPY_INCLUDE_DIRS=/usr/lib/python<version>/dist-packages/numpy/core/include/
     ```
   - For Python 2, replace `PYTHON3_` with `PYTHON2_`.

5. **Compile the Code**:

   - Run the `make` command from the build directory, optionally specifying the number of parallel jobs:
     ```bash
     make -j7
     ```

6. **Using OpenCV in Your Projects**:
   - In your CMake-based projects, use:
     ```cmake
     find_package(OpenCV)
     ```
   - Specify the path to the OpenCV build or install directory with `OpenCV_DIR`.

### Notes

- **Xcode and Git**: If Xcode and its command line tools are installed, Git is already available.
- **Package Managers**: You can also use Homebrew or pip to install stable releases of OpenCV, but this guide focuses on building from source.

---

By following these detailed steps, you can successfully install and build OpenCV on your macOS system.

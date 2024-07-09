<h1><div align="center">Installation</div></h1>

<table>
<tr>
  <th>Original author	</th>
  <th>@sajarindider</th>
</tr>
  <tr>
    <td>Compatibility</td>
    <td>OpenCV >= 3.4</td>
  </tr>
</table>

`The following steps have been tested for MacOSX (Mavericks) but should work with other versions as well.`

## Required Packages

- CMake 3.9 or higher
- Git
- Python 2.7 or later and Numpy 1.5 or later

This tutorial will assume you have [Python](https://docs.python.org/3/using/mac.html), [Numpy](https://docs.scipy.org/doc/numpy-1.10.1/user/install.html) and [Git](https://www.atlassian.com/git/tutorials/install-git) installed on your machine.

`Note`:
1. OSX comes with Python 2.7 by default, you will need to install `Python 3.8` if you want to use it specifically.
2. If you XCode and XCode Command Line-Tools installed, you already have git installed on your machine.
3. You also have to set XCode permissions

### Steps to Resolve the Issue:

1. **Agree to Xcode License**:
   Open a Terminal window and run the following command to agree to the Xcode license agreements:

   ```bash
   sudo xcodebuild -license
   ```

   Follow the prompts to read and agree to the license terms.

2. **Install Xcode Command Line Tools** (if not already installed):
   You can install the Xcode command line tools by running:

   ```bash
   xcode-select --install
   ```

### Detailed Steps:

1. **Open Terminal**:
   You can open the Terminal application from your Applications folder or by using Spotlight search.

2. **Agree to the Xcode License**:
   In the Terminal, run the command to agree to the Xcode license:

   ```bash
   sudo xcodebuild -license
   ```

   You will be prompted to enter your password. After that, read through the license agreement and follow the instructions to agree to it.

3. **Install Command Line Tools** (if needed):
   If you haven't installed the Xcode command line tools, you can install them by running:

   ```bash
   xcode-select --install
   ```

   Follow the prompts to complete the installation.

After completing these steps, try configuring and building OpenCV again using CMake.

## Installing CMake
1. Find the version for your system and download CMake from their release's [page](https://cmake.org/download/)
2. Install the dmg package and launch it from Applications. That will give you the UI app of CMake
3. From the CMake app window, choose menu Tools --> How to Install For Command Line Use. Then, follow the instructions from the pop-up there.
4. Install folder will be /usr/bin/ by default, submit it by choosing Install command line links.
5. Test that it works by running
```
cmake --version
```
`Note`:

You can use [Homebrew](https://brew.sh/) to install CMake with
``` 
brew install cmake
```
## Getting OpenCV Source Code

You can use the latest stable OpenCV version or you can grab the latest snapshot from our[Git repository](https://github.com/opencv/opencv).

### Getting the Latest Stable OpenCV Version
- Go to our downloads page.
- Download the source archive and unpack it.
### Getting the Cutting-edge OpenCV from the Git Repository
Launch Git client and clone [OpenCV repository](https://github.com/opencv/opencv). If you need modules from [OpenCV contrib repository](https://github.com/opencv/opencv_contrib) then clone it as well.

For example
```
cd ~/<my_working_directory>
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
```
## Building OpenCV from Source Using CMake
1. **Create a temporary directory**, which we denote as `build_opencv`, where you want to put the generated Makefiles, project files as well the object files and output binaries and enter there.

For example
```
mkdir build_opencv
cd build_opencv
```
`Note` :

It is good practice to keep clean your source code directories. Create build directory outside of source tree.
2.**Configuring**. Run `cmake [<some optional parameters>] <path to the OpenCV source directory>`

For example
```
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_EXAMPLES=ON ../opencv
```
or cmake-gui

- set the OpenCV source code path to, e.g. `/home/user/opencv`
- set the binary build path to your CMake build directory, e.g. `/home/user/build_opencv`
- set optional parameters
- run: "Configure"
- run: "Generate"
3. **Description of some parameters**
- build type: `CMAKE_BUILD_TYPE=Release `(or `Debug`)
- to build with modules from opencv_contrib set` OPENCV_EXTRA_MODULES_PATH` to `<path to opencv_contrib>/modules`
- set `BUILD_DOCS=ON` for building documents (doxygen is required)
- set `BUILD_EXAMPLES=ON` to build all examples
4. **[optional] Building python**. Set the following python parameters:
- `PYTHON3_EXECUTABLE = <path to python>`
- `PYTHON3_INCLUDE_DIR = /usr/include/python<version>`
- `PYTHON3_NUMPY_INCLUDE_DIRS = /usr/lib/python<version>/dist-packages/numpy/core/include/`

`Note` :

To specify Python2 versions, you can replace PYTHON3_ with PYTHON2_ in the above parameters.

5. **Build** From build directory execute make, it is recommended to do this in several threads

For example

```
make -j7 # runs 7 jobs in parallel
```
6. **To use OpenCV in your CMake-based projects through** `find_package(OpenCV)` specify `OpenCV_DIR=<path_to_build_or_install_directory>` variable.

`Note` :

You can also use a package manager like [Homebrew](https://brew.sh/) or [pip](https://pip.pypa.io/en/stable/) to install releases of OpenCV only (Not the cutting edge).

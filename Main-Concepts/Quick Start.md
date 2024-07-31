## Quick Start: Building Core Modules

### Step-by-Step Instructions

#### Install Minimal Prerequisites (Ubuntu 18.04 as Reference)
To start building OpenCV, you need to install the necessary tools and dependencies:
```bash
sudo apt update && sudo apt install -y cmake g++ wget unzip
```
- `cmake`: A tool to manage the build process.
- `g++`: The GNU C++ compiler.
- `wget`: A tool to download files from the web.
- `unzip`: A utility to extract zip files.

#### Download and Unpack Sources
Download the OpenCV source code:
```bash
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.x.zip
unzip opencv.zip
```

#### Create Build Directory
Create a directory for building the library and navigate into it:
```bash
mkdir -p build && cd build
```

#### Configure
Generate the build files using CMake:
```bash
cmake ../opencv-4.x
```
- This step configures the build environment according to your system and prepares the necessary files.

#### Build
Compile the OpenCV library:
```bash
cmake --build .
```
- This command compiles the OpenCV source code into binary executables and libraries.

---

## Building with `opencv_contrib` Modules

### Step-by-Step Instructions

#### Install Minimal Prerequisites (Ubuntu 18.04 as Reference)
Ensure you have the required tools installed:
```bash
sudo apt update && sudo apt install -y cmake g++ wget unzip
```

#### Download and Unpack Sources
Download the core OpenCV and the extra modules:
```bash
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.x.zip
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.x.zip
unzip opencv.zip
unzip opencv_contrib.zip
```

#### Create Build Directory and Switch Into It
Create a build directory and navigate into it:
```bash
mkdir -p build && cd build
```

#### Configure
Configure the build to include the extra modules:
```bash
cmake -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib-4.x/modules ../opencv-4.x
```
- `-DOPENCV_EXTRA_MODULES_PATH` specifies the path to the extra modules.

#### Build
Compile the OpenCV library with the extra modules:
```bash
cmake --build .
```

---

## Detailed Process

### Installation Overview
Refer to the [OpenCV installation overview tutorial](https://docs.opencv.org/master/d0/d3d/tutorial_general_install.html) for general installation details and [OpenCV configuration options reference](https://docs.opencv.org/master/db/d05/tutorial_config_reference.html) for configuration options.

### Install Compiler and Build Tools
To compile OpenCV, you need a C++ compiler and build tools:

#### Install GCC or Clang
```bash
sudo apt install -y g++
```
or
```bash
sudo apt install -y clang
```

#### Install CMake
```bash
sudo apt install -y cmake
```

#### Install Make or Ninja
```bash
sudo apt install -y make
```
or
```bash
sudo apt install -y ninja-build
```

#### Install Tools for Downloading and Unpacking Sources
```bash
sudo apt install -y wget unzip
```
or
```bash
sudo apt install -y git
```

### Download Sources
You can get OpenCV sources in two ways:

#### Using wget and unzip
```bash
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.x.zip
unzip opencv.zip
mv opencv-4.x opencv
```

#### Using git
```bash
git clone https://github.com/opencv/opencv.git
git -C opencv checkout 4.x
```
- Cloning the repository includes the full change history, which can be useful for development.

### Configure and Build

#### Create Build Directory
```bash
mkdir -p build && cd build
```

#### Configure
Generate build scripts for the preferred build system:

##### For Make
```bash
cmake ../opencv
```

##### For Ninja
```bash
cmake -GNinja ../opencv
```

#### Build
Run the actual compilation process:

##### Using Make
```bash
make -j4
```
- `-j4` allows make to run 4 compilation processes in parallel.

##### Using Ninja
```bash
ninja
```
- Ninja automatically detects the number of available processor cores.

### Check Build Results
After a successful build, you will find libraries in the `build/lib` directory and executables in the `build/bin` directory:
```bash
ls bin
ls lib
```
CMake package files will be located in the build root:
```bash
ls OpenCVConfig*.cmake
ls OpenCVModules.cmake
```

### Install
#### Warning
The installation process only copies files to predefined locations and does minor patching. Installing using this method does not integrate OpenCV into the system package registry and thus, for example, OpenCV cannot be uninstalled automatically.

#### Default Installation
By default, OpenCV will be installed to the `/usr/local` directory. Installation should be performed with elevated privileges:
```bash
sudo make install
```
or
```bash
sudo ninja install
```

#### Custom Installation Path
To install to a different directory, use the `CMAKE_INSTALL_PREFIX` parameter:
```bash
cmake -DCMAKE_INSTALL_PREFIX=$HOME/.local ..
make -j4
sudo make install
```

Refer to the [OpenCV configuration options reference](https://docs.opencv.org/master/db/d05/tutorial_config_reference.html) for detailed configuration options.

### Troubleshooting
- **Recreate Build Directory**: If you experience problems, try to clean or recreate the build directory.
- **Multiple Jobs**: Make can run multiple compilation processes in parallel, and Ninja will automatically detect the number of available processor cores.

---
[![Made by](https://img.shields.io/badge/Writer-Shyama-6b5b95?style=for-the-badge)](https://github.com/shyama7004)

### Directory Structure

```
/my........opencv repo
├── Dockerfile
├── test.py
├── charuco_board.png
└── other_files_or_directories
```


<details>
<summary>Dokcerfile</summary>

```bash
# Base image
FROM ubuntu:22.04

# Set non-interactive mode for apt-get
ENV DEBIAN_FRONTEND=noninteractive

# Install required packages
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    libgtk-3-dev \
    libavcodec-dev \
    libavformat-dev \
    libswscale-dev \
    libtbb-dev \
    libjpeg-dev \
    libpng-dev \
    libtiff-dev \
    libv4l-dev \
    libxvidcore-dev \
    libx264-dev \
    libatlas-base-dev \
    gfortran \
    python3 \
    python3-dev \
    python3-pip \
    && apt-get clean

# Install Python dependencies
RUN pip3 install numpy

# Set PYTHONPATH to include the OpenCV Python bindings
ENV PYTHONPATH=/usr/local/lib/python3.10/site-packages:$PYTHONPATH

# Build and install OpenCV
WORKDIR /opencv
COPY . .
RUN mkdir build && cd build && \
    cmake \
    -D CMAKE_BUILD_TYPE=Release \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D PYTHON3_EXECUTABLE=$(which python3) \
    -D PYTHON3_INCLUDE_DIR=$(python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
    -D PYTHON3_PACKAGES_PATH=/usr/local/lib/python3.10/site-packages \
    -D BUILD_PERF_TESTS=OFF \
    -D BUILD_TESTS=OFF \
    -D BUILD_opencv_gapi=OFF \
    -D BUILD_opencv_aruco=ON \
    -D BUILD_opencv_python3=ON \
    -D BUILD_opencv_java=OFF \
    -D BUILD_opencv_js=OFF \
    .. && \
    make -j2 && \
    make install && \
    ldconfig

# Verify OpenCV installation
RUN python3 -c "import cv2; print('OpenCV version:', cv2.__version__); print('aruco' in dir(cv2))"

# Set the working directory for the script execution
WORKDIR /output

# Copy the test script to the container
COPY test.py /output

# Run the test script
CMD ["python3", "test.py"]
```
</details>

<details>
<summary>Commands</summary>

#### Step - 1 :

```bash
sudo docker build -t opencv-test .

```

#### Step - 2 :

```bash
sudo docker run --rm -v $(pwd):/output opencv-test
```
</details>

<details>
<summary>test.py</summary>

```py
import cv2
import numpy as np
import os

# Constants for Charuco board and ArUco marker generation
ARUCO_DICT = cv2.aruco.DICT_5X5_100
SQUARES_VERTICALLY = 7
SQUARES_HORIZONTALLY = 5
SQUARE_LENGTH = 0.035  # in meters
MARKER_LENGTH = 0.025  # in meters

def load_image(image_path):
    """Loads an image from the specified path for Charuco detection."""
    img = cv2.imread(image_path)
    if img is None:
        print("Failed to load image. Check the path:", image_path)
    return img

def create_charuco_board():
    """Creates a Charuco board object and returns it with its dictionary."""
    aruco_dict = cv2.aruco.getPredefinedDictionary(ARUCO_DICT)
    board = cv2.aruco.CharucoBoard(
        (SQUARES_HORIZONTALLY, SQUARES_VERTICALLY),
        SQUARE_LENGTH,
        MARKER_LENGTH,
        aruco_dict
    )
    return board

def run_charuco_detection(img, board):
    """Detects ArUco markers and interpolates Charuco corners using CharucoDetector."""
    try:
        # Create the Charuco detector
        charuco_detector = cv2.aruco.CharucoDetector(board)

        # Detect markers and Charuco corners in one step
        charuco_corners, charuco_ids, marker_corners, marker_ids = charuco_detector.detectBoard(img)

        # Check if detection was successful
        if charuco_corners is not None and charuco_ids is not None:
            print("Charuco corners detected:", charuco_corners)
            print("Charuco IDs detected:", charuco_ids)

            # Visualize results
            img_marked = cv2.aruco.drawDetectedMarkers(np.copy(img), marker_corners, marker_ids)
            img_charuco_marked = cv2.aruco.drawDetectedCornersCharuco(np.copy(img), charuco_corners, charuco_ids)

            # Ensure the output directory exists
            output_dir = "/output"
            os.makedirs(output_dir, exist_ok=True)

            # Save images to the specified directory
            cv2.imwrite(os.path.join(output_dir, "detected_markers.png"), img_marked)
            cv2.imwrite(os.path.join(output_dir, "detected_charuco_corners.png"), img_charuco_marked)
            print("Images saved to the specified directory for debugging.")
        else:
            print("Charuco corner interpolation failed.")

    except Exception as e:
        print(f"Error during Charuco detection: {e}")

# Path to the pre-existing image relative to the current directory
image_path = 'charuco_board.png'  # Update with the relative path you mentioned

# Load the image and create Charuco board for detection
img = load_image(image_path)
if img is not None:
    board = create_charuco_board()
    run_charuco_detection(img, board)
else:
    print("Image loading failed.")
```

</details>

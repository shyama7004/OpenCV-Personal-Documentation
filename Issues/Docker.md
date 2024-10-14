<details>
<summary>Docker Testing</summary>
  
### Dockerfile Directory Structure:)

```

├── opencv/               # OpenCV source code repo
│   ├── Dockerfile        # Dockerfile placed inside the opencv folder
│   ├── index.py          # Python script to be tested
│   ├── 32.png            # Image file to be used in the Python script
│   └── ...               # Other OpenCV repo files and folders
```

### Dockerfile:

```Dockerfile
# Use Python 3.10 as a base image
FROM python:3.10-slim

# Install dependencies to build OpenCV
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    python3-dev \
    libgtk-3-dev \
    pkg-config \
    libavcodec-dev \
    libavformat-dev \
    libswscale-dev \
    libtbbmalloc2 \
    libtbb-dev \
    libjpeg-dev \
    libpng-dev \
    libtiff-dev \
    libv4l-dev \
    libxvidcore-dev \
    libx264-dev \
    libatlas-base-dev \
    gfortran \
    libgl1-mesa-glx \
    libglib2.0-0 \
    && apt-get clean

# Set the working directory in the container
WORKDIR /app

# Copy your OpenCV repo into the Docker container
COPY ./ /app/opencv

# Install NumPy (required by OpenCV and your script)
RUN pip install numpy

# Build OpenCV from your modified source, ensuring Python bindings are built
WORKDIR /app/opencv
RUN mkdir build && cd build \
    && cmake -D CMAKE_BUILD_TYPE=Release \
             -D CMAKE_INSTALL_PREFIX=/usr/local \
             -D BUILD_opencv_python3=ON \
             -D BUILD_opencv_gapi=OFF \
             -D PYTHON_EXECUTABLE=$(which python3) \
    .. \
    && make -j$(nproc) \
    && make install

# Set the working directory to where the Python script is
WORKDIR /app

COPY ./index.py /app/index.py
COPY ./32.png /app/32.png

# Run the Python script
CMD ["python3", "/app/index.py"]

```

### Caution :

- Replace the following lines in index.py:

```python
cv2.imshow("src", src)
cv2.imshow("dst1", dst1)
cv2.imshow("dst2", dst2)
cv2.waitKey()
cv2.destroyAllWindows()
```

With:

```python
cv2.imwrite("src.png", src)
cv2.imwrite("dst1.png", dst1)
cv2.imwrite("dst2.png", dst2)
```
- This will save the images to files, and you can view them later.

### Steps to Build and Run:

1. **Build the Docker image**:
   ```bash
   docker build -t opencv-test .
   ```

2. **Run the Docker container**:
   ```bash
   docker run -v /Users/sankarsanbisoyi/Desktop/Lex/opencv:/app opencv-test

   ```

Target issues 

### Issue Link : [Click-Here](https://github.com/opencv/opencv/issues/25974)
### Issue Link : [Click-Here](https://github.com/opencv/opencv/issues/26179)
### Issue Link : [Click-Here](https://github.com/opencv/opencv/issues/26315)

<details>
  <summary>Issue-solving-approach(enhanced by GPT-40:) )</summary>
  
#### 1. **Modify the Dockerfile to Install GoogleTest**

Add the commands to clone and install GoogleTest inside your Dockerfile. This way, `gtest` will be installed during the Docker image build process.

Here’s the updated Dockerfile:

```dockerfile
# Use Python 3.10 as a base image
FROM python:3.10-slim

# Install dependencies to build OpenCV and GoogleTest
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

# Install GoogleTest
RUN git clone https://github.com/google/googletest.git /app/googletest \
    && mkdir -p /app/googletest/build \
    && cd /app/googletest/build \
    && cmake .. \
    && make -j$(nproc) \
    && make install

# Copy your OpenCV repo into the Docker container
COPY ./ /app/opencv

# Install NumPy (required by OpenCV and your script)
RUN pip install numpy

# Build OpenCV from your modified source, ensuring Python bindings and tests are built
WORKDIR /app/opencv
RUN mkdir build && cd build \
    && cmake -D CMAKE_BUILD_TYPE=Release \
             -D CMAKE_INSTALL_PREFIX=/usr/local \
             -D BUILD_opencv_python3=ON \
             -D BUILD_opencv_gapi=OFF \
             -D BUILD_TESTS=ON \   # Enable tests
             -D PYTHON_EXECUTABLE=$(which python3) \
    .. \
    && make -j$(nproc) \
    && make install

# Set the working directory to where the Python script is
WORKDIR /app

COPY ./index.py /app/index.py
COPY ./32.png /app/32.png

# Command to run tests (can be adjusted if needed)
CMD ["make", "test"]
```

This updated Dockerfile includes the following changes:
- **GoogleTest installation**:
  - It clones the GoogleTest repository, builds it using CMake, and installs it on the system. 
  - This ensures that GoogleTest is available when running the tests.
  
#### 2. **Build the Docker Image**
After updating the Dockerfile, you need to build the Docker image that includes GoogleTest and OpenCV.

Run this command from the directory where your `Dockerfile` is located (inside your `opencv/` directory):

```bash
docker build -t opencv-test .
```

This command does the following:
- Downloads the base image (`python:3.10-slim`).
- Installs all dependencies, including OpenCV and GoogleTest.
- Copies your OpenCV code and other assets into the container.
- Builds OpenCV with test support enabled.

#### 3. **Run the Docker Container**
Once the Docker image is built, run the container to compile and execute the tests.

```bash
docker run --rm opencv-test
```

This command will:
- Launch the Docker container.
- Compile the OpenCV project, including the tests.
- Run the tests using `make test`.

If you’ve added any C++ test cases, they should be executed automatically, and the results will be shown in the container logs.

### 4. **Next Steps: Debugging and Development**
- **Inspect logs**: The container will output the test results directly to the console. If any tests fail, you can debug the issue within the container.
- **Modifications**: If you need to modify test cases or add new ones, update the source code locally and rebuild the Docker image to reflect those changes.
- **Run tests interactively**: If you need to run the tests interactively or troubleshoot inside the container, you can start the container in an interactive mode:
  ```bash
  docker run -it opencv-test /bin/bash
  ```

</details>

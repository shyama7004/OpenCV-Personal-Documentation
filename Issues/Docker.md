### Dockerfile Directory Structure:)

```

├── opencv/               # OpenCV source code repo
│   ├── Dockerfile        # Dockerfile placed inside the opencv folder
│   ├── index.py          # Python script to be tested
│   ├── 32.png            # Image file to be used in the Python script
│   └── ...               # Other OpenCV repo files and folders
```
<details>
  <summary>Normal testing</summary>

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
</details>

<details>
<summary>Dockerfile with google test</summary>

```bash
# Base image
FROM ubuntu:22.04

# Set non-interactive mode for apt-get
ENV DEBIAN_FRONTEND=noninteractive

# Update package lists and install required packages
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    python3 \
    python3-pip \
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
    libgtest-dev \
    libopencv-dev \
    && apt-get clean

# Confirm Python version
RUN python3 --version

# Manually build GoogleTest with shared libraries
RUN if [ ! -d "/usr/src/googletest" ]; then \
        mkdir -p /usr/src/googletest; \
    fi && \
    cd /usr/src/googletest && \
    rm -rf * && \
    git clone https://github.com/google/googletest.git . && \
    cmake -DBUILD_SHARED_LIBS=ON . && \
    make && \
    cp lib/*.so /usr/lib && \
    cp -r googletest/include/gtest /usr/include

# Set the working directory to /app/opencv
WORKDIR /app/opencv

# Copy the local OpenCV repository into the Docker container
COPY . /app/opencv/

# Clean any previous builds and create a new build directory
RUN rm -rf build && mkdir build && cd build \
    && cmake -D CMAKE_BUILD_TYPE=Release \
             -D CMAKE_INSTALL_PREFIX=/usr/local \
             -D BUILD_opencv_python3=ON \
             -D BUILD_opencv_gapi=OFF \
             -D BUILD_opencv_java=OFF \
             -D BUILD_opencv_objc=OFF \
             -D PYTHON_EXECUTABLE=$(which python3) \
             -D BUILD_TESTS=ON \
             -D CMAKE_VERBOSE_MAKEFILE=ON .. \
    && make -j$(nproc) \
    && make install

# Copy the test file and the image for the test
COPY test_orb.cpp /app/opencv/build/
COPY images/tsukuba.png /app/opencv/images/

# Compile the test executable with OpenCV and GoogleTest, explicitly linking the necessary libraries
RUN cd /app/opencv/build && \
    g++ -o runTests /app/opencv/build/test_orb.cpp -lgtest -lgtest_main -lpthread -L/usr/lib -I/usr/include/gtest `pkg-config --cflags --libs opencv4`

# Set LD_LIBRARY_PATH to include the path for GoogleTest shared libraries
ENV LD_LIBRARY_PATH=/usr/lib:$LD_LIBRARY_PATH

# Entry point to run the tests
CMD ["sh", "-c", "cd /app/opencv/build && ./runTests /app/opencv/images/tsukuba.png"]


```

- Click here : [Link](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html) : )

<details>
  <summary>`libgtest.so` problem</summary>

#### 1. Verify if libgtest.so exists in the container

First, check if libgtest.so was successfully built and copied to the correct directory:

```bash
docker run -it --rm opencv-test bash
```

#### Once inside the container, run:

```bash
ls /usr/lib | grep gtest
```

This should show libgtest.so and other related files. If libgtest.so is not there, then the GoogleTest build step may have failed.

#### 2. While still inside the container, navigate to your build directory and try running the test executable:

```bash
cd /app/opencv/build
```
#### 3. Create the symbolic link inside the container

Once you're inside the container's bash shell, use ln with elevated privileges to create the symbolic link:

```bash
ln -s /usr/lib/libgtest.so /usr/lib/libgtest.so.1.15.2
```
#### 4. Verify the link

After creating the symlink, confirm that it exists and points to the correct file:

```bash
ls -l /usr/lib | grep gtest
```

#### You should see an output like:

```javascript
libgtest.so.1.15.2 -> /usr/lib/libgtest.so
```

#### Run this

```bash
./runTests
```

  </details>

---

</details>

<details>
  <summary>Linux x64 Debug</summary>

  ```
# Use Python 3.10 as a base image for Linux x64 Debug
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

WORKDIR /app

# Copy your OpenCV repo into the Docker container
COPY ./ /app/opencv

# Install NumPy and pytest (required by OpenCV and for testing)
RUN pip install numpy pytest

# Build OpenCV in Debug mode
WORKDIR /app/opencv
RUN mkdir build && cd build \
    && cmake -D CMAKE_BUILD_TYPE=Debug \
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

# Copy regression test script (ensure this file exists in your local OpenCV directory)
COPY ./test_draw_contours.py /app/test_draw_contours.py

# Run the regression tests using pytest
CMD ["pytest", "/app/test_draw_contours.py"]

  
  ```
  
</details>
<details>
<summary>Ubuntu testing(Basic)</summary>
  
```
# Use Ubuntu 22.04 base image
FROM ubuntu:22.04

# Install dependencies for OpenCV
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    python3 \
    python3-pip \
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
    && apt-get clean

# Set working directory
WORKDIR /app

# Copy OpenCV repo
COPY ./ /app/opencv

# Install NumPy
RUN pip install numpy

# Build OpenCV
WORKDIR /app/opencv
RUN mkdir build && cd build \
    && cmake -D CMAKE_BUILD_TYPE=Release \
             -D CMAKE_INSTALL_PREFIX=/usr/local \
             -D WITH_OPENCL=OFF \
             -D BUILD_opencv_python3=ON \
    .. \
    && make -j4 \
    && make install

# Set working directory and run the script
WORKDIR /app
COPY ./index.py /app/index.py
CMD ["python3", "/app/index.py"]
```
</details>

<details>
  <summary>Ubuntu testing(With regression tests)</summary>

```
# Use Python 3.10 as a base image
FROM python:3.10-slim

# Install dependencies to build OpenCV and pytest
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

# Install NumPy and pytest (required by OpenCV and for testing)
RUN pip install numpy pytest

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

# Copy regression test script (ensure this file exists in your local OpenCV directory)
COPY ./test_draw_contours.py /app/test_draw_contours.py

# Run the regression tests using pytest
CMD ["pytest", "/app/test_draw_contours.py"]

```

## Remember

### File Placement
- Create `test_draw_contours.py` : You can create the `test_draw_contours.py` file in your local OpenCV directory.
- This way, when you build the Docker image, it will copy the test file into the container.

### Location: The test file should be in the same directory from which you build the Docker image, or adjust the COPY command in the Dockerfile to point to the correct location.

- Running the Tests
- When you build and run the Docker container, pytest will execute the tests defined in test_draw_contours.py. Make sure your test file is correctly set up to validate the drawContours functionality.

</details>

<details>
<summary>Windows</summary>

### 1. **Create a GitHub Repository**

1. If you haven’t already, go to [GitHub](https://github.com) and create a new repository for your project:
   - Click the "New" button to create a new repository.
   - Give it a name (e.g., `opencv-test-windows`).
   - Set it to "Public" or "Private" as needed.

2. Clone the repository to your local machine:
   ```bash
   git clone https://github.com/<your-username>/opencv-test-windows.git
   ```

### 2. **Add Your Files to the Repository**

Add the necessary files for the OpenCV project:
- The Dockerfile (if using).
- Your Python scripts (`index.py`, `test_draw_contours.py`).
- Any resources like images (e.g., `32.png`).

Example of organizing the files:
```
opencv-test-windows/
├── Dockerfile
├── index.py
├── test_draw_contours.py
├── 32.png
```

Commit these files to your GitHub repository:
```bash
git add .
git commit -m "Initial commit with OpenCV test scripts"
git push origin main
```

### 3. **Set Up GitHub Actions Workflow**

1. In your repository, navigate to the **Actions** tab.
2. You’ll see a suggestion to **set up a workflow**. Click **"Set up a workflow yourself"**.
3. This will open the editor where you can add a workflow YAML file.

Here’s the YAML configuration to test on **Windows**:

```yaml
name: Test on Windows

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  windows_test:
    runs-on: windows-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2
      
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.10'

    - name: Install dependencies
      run: |
        pip install numpy pytest

    - name: Run OpenCV Tests
      run: |
        pytest test_draw_contours.py
```

4. Save this as `.github/workflows/windows_test.yml`.

### 4. **Understanding the Workflow File**

- **Trigger Events (`on`)**: This specifies that the workflow will run when code is pushed to the `main` branch or when a pull request is made to `main`.
  
- **Job (`windows_test`)**: The job defines everything needed to set up and run your test on a **Windows environment**.
  
  - `runs-on: windows-latest`: Specifies that the workflow should run on a Windows machine.
  
  - **Checkout repository**: This uses the `actions/checkout@v2` action to check out your repository code into the runner so the tests can be run on the files.
  
  - **Set up Python**: This uses the `actions/setup-python@v2` action to set up Python 3.10 on the Windows runner.
  
  - **Install dependencies**: This installs `numpy` and `pytest`—two required dependencies for your OpenCV testing.

  - **Run OpenCV Tests**: This runs your test file `test_draw_contours.py` using `pytest`.

### 5. **Push the Workflow to GitHub**

Once you’ve created the `windows_test.yml` file and saved it under `.github/workflows/`, push it to your GitHub repository:

```bash
git add .github/workflows/windows_test.yml
git commit -m "Add GitHub Actions workflow for Windows testing"
git push origin main
```

### 6. **Run the Workflow**

After you push the workflow, go back to your repository on GitHub and navigate to the **Actions** tab:
- You should see a new workflow running under the name "Test on Windows."
- Click on the workflow to see the details and logs.

GitHub Actions will:
1. Spin up a Windows runner.
2. Set up Python 3.10.
3. Install `numpy` and `pytest`.
4. Run the test file.

You can view the live logs to see the progress.

### 7. **Check Results**

Once the workflow completes, you’ll see the status of your tests—whether they passed or failed.

- If all tests pass, you’ll see a green checkmark next to the workflow run.
- If there’s a failure, you’ll see a red cross and can click on the failed step to view logs and debug.

### 8. **Update and Rerun**

If there are any issues, you can modify the code or workflow, commit the changes, and push them to GitHub. The workflow will rerun automatically on push.

---

### Summary of Steps:
1. Create a GitHub repository and add your OpenCV-related files.
2. Add the GitHub Actions workflow YAML for Windows testing.
3. Push the workflow to the repository.
4. View the test results in the Actions tab.

  
</details>

<details>
  <summary>Explanation of Regression Funda</summary>

  
### 1. **Set Up the Dockerfile**

The Dockerfile is a script that defines the environment and steps needed to build your application. Here’s a breakdown of the significant sections relevant to running regression tests:

#### a. **Base Image and Dependencies**
```dockerfile
# Use Python 3.10 as a base image
FROM python:3.10-slim
```
- This line specifies that the base image for the Docker container should be Python 3.10 in a slim format, which is lightweight and efficient.

#### b. **Install Required Packages**
```dockerfile
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
```
- This section updates the package list and installs essential libraries and tools required to build OpenCV and run your tests. 

#### c. **Set Working Directory**
```dockerfile
WORKDIR /app
```
- Sets the working directory in the container to `/app`. All subsequent commands will be executed in this directory.

#### d. **Copy the OpenCV Code**
```dockerfile
COPY ./ /app/opencv
```
- This command copies the contents of your local OpenCV codebase into the Docker container.

#### e. **Install Python Packages**
```dockerfile
RUN pip install numpy pytest
```
- Installs Python packages `numpy` (needed by OpenCV) and `pytest` (used for running the regression tests).

#### f. **Build OpenCV**
```dockerfile
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
```
- This section builds the OpenCV library from source, ensuring that Python bindings are included.

#### g. **Copy Test Script**
```dockerfile
COPY ./test_draw_contours.py /app/test_draw_contours.py
```
- Copies the regression test script `test_draw_contours.py` from your local filesystem to the Docker container.

#### h. **Run Tests Using CMD**
```dockerfile
CMD ["pytest", "/app/test_draw_contours.py"]
```
- Sets the default command to run when the container starts, which is to execute `pytest` on the test script.

### 2. **Create the Regression Test Script**

You need to create the `test_draw_contours.py` file, which contains the actual test cases to validate the functionality of the `drawContours` method:

```python
import cv2
import numpy as np

def test_draw_contours():
    # Create a blank image
    image = np.zeros((500, 500, 3), dtype=np.uint8)

    # Create a simple contour
    contour = np.array([[100, 100], [200, 100], [200, 200], [100, 200]], dtype=np.int32)
    contours = [contour]

    # Create a hierarchy (no child contours)
    hierarchy = np.array([[-1, -1, -1, -1]], dtype=np.int32)

    # Draw contours
    cv2.drawContours(image, contours, 0, (255, 0, 0), thickness=2, lineType=cv2.LINE_AA, hierarchy=hierarchy)

    # Check if the contour is drawn correctly
    assert np.any(image[150, 150] == [255, 0, 0])  # Check that a pixel in the contour is red
    assert np.all(image[50:250, 50:250] == [0, 0, 0])  # Ensure outside of the contour is black

if __name__ == "__main__":
    test_draw_contours()
```
- This script creates a blank image, defines a simple contour, and uses `cv2.drawContours` to draw it. Assertions check that the contour is drawn correctly.

### 3. **Build the Docker Image**
To create the Docker image from the Dockerfile, run:
```bash
docker build -t my_opencv_image .
```
- This command builds the image with the tag `my_opencv_image`. The `.` indicates that the Dockerfile is in the current directory.

### 4. **Run the Docker Container**
To run the regression tests, you can use:
```bash
docker run --rm my_opencv_image
```
- This command starts a new container from the `my_opencv_image`, and the tests will run automatically due to the `CMD` specified in the Dockerfile.

### 5. **Optional: Run Tests Manually**
If you want to interactively run the tests:
```bash
docker run -it --rm my_opencv_image /bin/bash
```
- Once inside the container, you can manually run:
```bash
pytest /app/test_draw_contours.py
```

### Summary of Steps
1. **Dockerfile Setup**: Create a Dockerfile to define the environment, install dependencies, copy files, and set commands.
2. **Test Script Creation**: Write a Python test script that validates the functionality of `drawContours`.
3. **Build the Docker Image**: Use `docker build` to create the image.
4. **Run the Tests**: Use `docker run` to execute the tests automatically or interactively.

This entire process ensures that your changes to the OpenCV codebase are validated by running automated tests in a consistent environment, making it easier to catch regressions or bugs early in the development process.
</details>

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
<details>
   <summary>Usefulness of Regression Testing</summary>


### What is Regression Testing?

**Regression Testing** is a type of software testing that ensures that recent changes (such as enhancements, bug fixes, or new features) haven't adversely affected the existing functionality of the software. The primary goal is to catch any unintended side effects introduced by changes, ensuring that previously working features continue to function as expected.

### Key Objectives of Regression Testing:
1. **Verify Functionality**: Ensure that previously developed and tested features still perform correctly after changes.
2. **Detect Bugs**: Identify bugs introduced inadvertently due to recent changes or new code.
3. **Increase Confidence**: Provide assurance that the new code does not disrupt the stability and reliability of the software.

### How Regression Testing is Used in Your Docker Setup

In the context of your OpenCV project, regression testing is implemented as follows:

1. **Identifying the Area of Change**:
   - In your case, you modified the `drawContours` function to improve its ability to draw child contours. This change could potentially affect how contours are rendered throughout the OpenCV library.

2. **Creating a Test Case**:
   - You wrote a regression test script (`test_draw_contours.py`) that specifically tests the functionality of the `drawContours` method. This script includes assertions to verify:
     - That a contour is drawn correctly on a blank image.
     - That the area outside the contour remains unaffected (i.e., still black).
   - Here’s a snippet of the test:
     ```python
     assert np.any(image[150, 150] == [255, 0, 0])  # Check that a pixel in the contour is red
     assert np.all(image[50:250, 50:250] == [0, 0, 0])  # Ensure outside of the contour is black
     ```

3. **Integrating Testing into the Build Process**:
   - The regression test script is included in the Docker container alongside your OpenCV code. This integration allows you to run the tests automatically as part of the build process or when the container is executed.

4. **Running Tests in a Consistent Environment**:
   - By using Docker, you ensure that the tests run in a consistent environment, eliminating issues that might arise due to differences in local setups. This consistency is critical for reliable regression testing.

5. **Continuous Validation**:
   - Whenever you rebuild the Docker image and run the container, the regression tests execute automatically. If any of the assertions in your test fail, it indicates that the recent changes to `drawContours` have introduced a bug or caused existing functionality to fail, thus requiring investigation and potential fixes.

### Benefits of Regression Testing in Your Case
- **Immediate Feedback**: You get immediate feedback on whether your changes are valid and do not break existing functionality.
- **Confidence in Code Quality**: It provides confidence in the stability of the OpenCV library after changes, making it easier to merge contributions and perform updates.
- **Easier Debugging**: If a test fails, it helps narrow down the potential area of concern to the changes you made, simplifying the debugging process.

### Summary
Regression testing is a vital practice in software development that helps maintain software quality and stability after changes. In your OpenCV project, it's implemented through a dedicated test script that verifies the correct functionality of the `drawContours` method after modifications, ensuring that new changes do not disrupt existing capabilities. This approach leads to more reliable software and a smoother development process.

</details>

<details>
<summary>Dockers-Cache</summary>

  
# Clearing Docker Cache

Clearing Docker cache can be necessary to free up disk space or resolve issues caused by stale images, containers, or other cached data. Here are some steps you can follow to clear different types of Docker cache:

## 1. Remove Unused Containers, Networks, Images, and Build Cache

Docker provides a prune command to remove unused data:

```bash
docker system prune -a
```

- **-a**: Remove all unused images, not just dangling ones.
- **--volumes**: Remove all unused volumes (add this option if you want to remove unused volumes as well).

## 2. Remove Specific Docker Objects

### Remove Unused Containers

List all containers (including stopped ones):
```bash
docker ps -a
```
Remove all stopped containers:
```bash
docker container prune
```
Remove a specific container:
```bash
docker rm <container_id>
```

### Remove Unused Images

List all images:
```bash
docker images -a
```
Remove all unused images:
```bash
docker image prune -a
```
Remove a specific image:
```bash
docker rmi <image_id>
```

### Remove Unused Networks

List all networks:
```bash
docker network ls
```
Remove all unused networks:
```bash
docker network prune
```
Remove a specific network:
```bash
docker network rm <network_id>
```

### Remove Unused Volumes

List all volumes:
```bash
docker volume ls
```
Remove all unused volumes:
```bash
docker volume prune
```
Remove a specific volume:
```bash
docker volume rm <volume_name>
```

## 3. Remove Docker Build Cache

Remove the build cache:
```bash
docker builder prune
```
To remove all build cache:
```bash
docker builder prune --all
```

## 4. Remove Everything (Use with Caution)

If you want to remove all Docker data, you can use the following command. This will remove all images, containers, volumes, and networks:
```bash
docker system prune -a --volumes
```

## Summary of Commands

1. **Prune unused data**:
```bash
docker system prune -a --volumes
```

2. **Remove specific containers, images, networks, or volumes**:
```bash
docker rm <container_id>
docker rmi <image_id>
docker network rm <network_id>
docker volume rm <volume_name>
```

3. **Prune specific types of unused data**:
```bash
docker container prune
docker image prune -a
docker network prune
docker volume prune
docker builder prune
```

By using these commands, you can manage and clear your Docker cache effectively.


</details>

# Getting Started with Videos

## Goal
- Learn to read video, display video, and save video.
- Learn to capture video from a camera and display it.
- You will learn these functions: `cv.VideoCapture()`, `cv.VideoWriter()`.

## Capture Video from Camera
Often, we have to capture live stream with a camera. OpenCV provides a very simple interface to do this. Let's capture a video from the camera (I am using the built-in webcam on my laptop), convert it into grayscale video, and display it. Just a simple task to get started.

To capture a video, you need to create a `VideoCapture` object. Its argument can be either the device index or the name of a video file. A device index is just the number to specify which camera. Normally one camera will be connected (as in my case). So I simply pass `0` (or `-1`). You can select the second camera by passing `1` and so on. After that, you can capture frame-by-frame. But at the end, don't forget to release the capture.

```python
import numpy as np
import cv2 as cv

cap = cv.VideoCapture(0)
if not cap.isOpened():
    print("Cannot open camera")
    exit()
while True:
    # Capture frame-by-frame
    ret, frame = cap.read()

    # if frame is read correctly ret is True
    if not ret:
        print("Can't receive frame (stream end?). Exiting ...")
        break
    # Our operations on the frame come here
    gray = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)
    # Display the resulting frame
    cv.imshow('frame', gray)
    if cv.waitKey(1) == ord('q'):
        break

# When everything done, release the capture
cap.release()
cv.destroyAllWindows()
```

`cap.read()` returns a bool (`True`/`False`). If the frame is read correctly, it will be `True`. So you can check for the end of the video by checking this returned value.

Sometimes, `cap` may not have initialized the capture. In that case, this code shows an error. You can check whether it is initialized or not by the method `cap.isOpened()`. If it is `True`, OK. Otherwise, open it using `cap.open()`.

You can also access some of the features of this video using the `cap.get(propId)` method where `propId` is a number from 0 to 18. Each number denotes a property of the video (if it is applicable to that video). Full details can be seen here: [cv::VideoCapture::get()](https://docs.opencv.org/3.4/d8/dfe/classcv_1_1VideoCapture.html#get).

Some of these values can be modified using `cap.set(propId, value)`. Value is the new value you want.

For example, I can check the frame width and height by `cap.get(cv.CAP_PROP_FRAME_WIDTH)` and `cap.get(cv.CAP_PROP_FRAME_HEIGHT)`. It gives me 640x480 by default. But I want to modify it to 320x240. Just use:

```python
ret = cap.set(cv.CAP_PROP_FRAME_WIDTH, 320)
ret = cap.set(cv.CAP_PROP_FRAME_HEIGHT, 240)
```

### Note
If you are getting an error, make sure your camera is working fine using any other camera application (like Cheese in Linux).

## Playing Video from File
Playing video from file is the same as capturing it from a camera, just change the camera index to a video file name. Also, while displaying the frame, use an appropriate time for `cv.waitKey()`. If it is too short, the video will be very fast, and if it is too long, the video will be slow (this is how you can display videos in slow motion). 25 milliseconds will be OK in normal cases.

```python
import numpy as np
import cv2 as cv

cap = cv.VideoCapture('vtest.avi')

while cap.isOpened():
    ret, frame = cap.read()

    # if frame is read correctly ret is True
    if not ret:
        print("Can't receive frame (stream end?). Exiting ...")
        break
    gray = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)

    cv.imshow('frame', gray)
    if cv.waitKey(1) == ord('q'):
        break

cap.release()
cv.destroyAllWindows()
```

### Note
Make sure a proper version of `ffmpeg` or `gstreamer` is installed. Sometimes it is a headache to work with video capture, mostly due to wrong installation of `ffmpeg`/`gstreamer`.

## Saving a Video
So we capture a video and process it frame-by-frame, and we want to save that video. For images, it is very simple: just use `cv.imwrite()`. Here, a little more work is required.

This time we create a `VideoWriter` object. We should specify the output file name (e.g., `output.avi`). Then we should specify the FourCC code (details in the next paragraph). Then, the number of frames per second (fps) and frame size should be passed. The last one is the `isColor` flag. If it is `True`, the encoder expects a color frame; otherwise, it works with grayscale frames.

FourCC is a 4-byte code used to specify the video codec. The list of available codes can be found on [fourcc.org](http://www.fourcc.org/). It is platform-dependent. The following codecs work fine for me:

- In Fedora: `DIVX`, `XVID`, `MJPG`, `X264`, `WMV1`, `WMV2`. (`XVID` is more preferable. `MJPG` results in high-size video. `X264` gives very small size video.)
- In Windows: `DIVX` (More to be tested and added)
- In OSX: `MJPG` (.mp4), `DIVX` (.avi), `X264` (.mkv).

FourCC code is passed as `cv.VideoWriter_fourcc('M','J','P','G')` or `cv.VideoWriter_fourcc(*'MJPG')` for `MJPG`.

The code below captures from a camera, flips every frame in the vertical direction, and saves the video.

```python
import numpy as np
import cv2 as cv

cap = cv.VideoCapture(0)

# Define the codec and create VideoWriter object
fourcc = cv.VideoWriter_fourcc(*'XVID')
out = cv.VideoWriter('output.avi', fourcc, 20.0, (640, 480))

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        print("Can't receive frame (stream end?). Exiting ...")
        break
    frame = cv.flip(frame, 0)

    # write the flipped frame
    out.write(frame)

    cv.imshow('frame', frame)
    if cv.waitKey(1) == ord('q'):
        break

# Release everything if job is finished
cap.release()
out.release()
cv.destroyAllWindows()
```

## Additional Resources

<details>
<summary>Exercises</summary>

### `cv::VideoCapture` Class Reference

`cv::VideoCapture` is a class used to capture video from files, image sequences, or cameras.

#### **Public Member Functions:**

- **VideoCapture()**  
  Default constructor.

- **VideoCapture(const String &filename, int apiPreference = CAP_ANY)**  
  Opens a video file or image sequence using the specified API.

- **VideoCapture(int index, int apiPreference = CAP_ANY)**  
  Opens a camera for video capturing using the specified API.

- **VideoCapture(const String &filename, int apiPreference, const std::vector<int>& params)**  
  Opens a video file or image sequence with additional parameters.

- **~VideoCapture()**  
  Destructor for closing the video file or capturing device.

- **bool open(const String &filename, int apiPreference = CAP_ANY)**  
  Opens a video file or image sequence.

- **bool open(int index, int apiPreference = CAP_ANY)**  
  Opens a camera for video capturing.

- **bool isOpened() const**  
  Checks if the capturing device is initialized.

- **void release()**  
  Closes the video file or capturing device.

- **bool grab()**  
  Grabs the next frame from the video file or capturing device.

- **bool retrieve(OutputArray image, int flag = 0)**  
  Decodes and returns the grabbed video frame.

- **bool read(OutputArray image)**  
  Grabs, decodes, and returns the next video frame.

- **bool set(int propId, double value)**  
  Sets a property of the video capturing device.

- **double get(int propId) const**  
  Returns the specified property of the video capturing device.

- **bool getBackendName() const**  
  Returns the name of the backend (API) in use.

- **bool retrieve(OutputArray image, int flag) const**  
  Retrieves the frame of the specified channel.

---

#### **Static Public Member Functions:**

- **static int getBackendType(const String &filename, int apiPreference)**  
  Returns the backend type used to handle the specified file.

- **static String getBackends()**  
  Returns a list of available backends.

---

#### **Properties:**

- **CAP_PROP_POS_MSEC**  
  Current position of the video file in milliseconds.

- **CAP_PROP_POS_FRAMES**  
  0-based index of the next frame to be decoded/captured.

- **CAP_PROP_POS_AVI_RATIO**  
  Relative position of the video file.

- **CAP_PROP_FRAME_WIDTH**  
  Width of the frames in the video stream.

- **CAP_PROP_FRAME_HEIGHT**  
  Height of the frames in the video stream.

- **CAP_PROP_FPS**  
  Frame rate of the video stream.

- **CAP_PROP_FOURCC**  
  4-character code of the codec.

- **CAP_PROP_FRAME_COUNT**  
  Number of frames in the video file.

- **CAP_PROP_FORMAT**  
  Format of the Mat objects returned by retrieve().

- **CAP_PROP_MODE**  
  Backend-specific value indicating the current capture mode.

- **CAP_PROP_BRIGHTNESS**  
  Brightness of the image (only for cameras).

- **CAP_PROP_CONTRAST**  
  Contrast of the image (only for cameras).

- **CAP_PROP_SATURATION**  
  Saturation of the image (only for cameras).

- **CAP_PROP_HUE**  
  Hue of the image (only for cameras).

- **CAP_PROP_GAIN**  
  Gain of the image (only for cameras).

- **CAP_PROP_EXPOSURE**  
  Exposure (only for cameras).

- **CAP_PROP_CONVERT_RGB**  
  Boolean flags indicating whether images should be converted to RGB.

- **CAP_PROP_WHITE_BALANCE_BLUE_U**  
  Blue u component of the white balance.

- **CAP_PROP_RECTIFICATION**  
  Rectification flag for stereo cameras.

---

### Example Usage:

```cpp
VideoCapture cap(0); // Open default camera
if (!cap.isOpened()) {
    std::cerr << "Error opening video stream or file" << std::endl;
    return -1;
}

Mat frame;
while (true) {
    cap >> frame;
    if (frame.empty())
        break;
    imshow("Frame", frame);
    if (waitKey(10) == 27)
        break;
}
cap.release();
destroyAllWindows();
```
</details>

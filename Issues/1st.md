<div align = "center"><h2>Steps - 1 to Steps - 10</h2></div>

### Step 1 to 2: **Understand the problem**.

### Step 3: **Search for GStreamer-related Code in OpenCV**

1. **Locate Video I/O Modules in OpenCV**:
   - OpenCV has a module called **`videoio`** which handles video input/output operations, including streaming with GStreamer.
   - You'll find these files in the OpenCV source repository, likely in `modules/videoio`.

2. **Look for GStreamer Code in `videoio`**:
   - GStreamer integration usually happens through specific backends in OpenCV.
   - Look for files related to GStreamer, such as:
     - `cap_gstreamer.cpp` or `gstreamer_capture.cpp`
   - These files are often responsible for the capture pipeline with GStreamer.

### Step 4: **Search for `GstBuffer *buffer` & `gst_buffer_map`  Code in OpenCV**
  - Initially  I did this 

```cpp
// Assuming width and height are 640x480 and 3 channels (RGB)
int width = 640;
int height = 480;
int channels = 3;

// Calculate the size of the buffer
gsize size = width * height * channels;  // Size for RGB image

// Create a new buffer for GStreamer
GstBuffer *buffer = gst_buffer_new_allocate(NULL, size, NULL);

// Map the buffer for reading
GstMapInfo info;
gst_buffer_map(buffer, &info, (GstMapFlags)GST_MAP_READ);

// If you are copying data from an existing image (like OpenCV image)
memcpy(info.data, (guint8*)image->imageData, size);  // Ensure `size` matches

// Unmap after copying the data
gst_buffer_unmap(buffer, &info);

// Set the GStreamer buffer properties
GST_BUFFER_DURATION(buffer) = duration;
GST_BUFFER_PTS(buffer) = timestamp;
GST_BUFFER_DTS(buffer) = timestamp;
GST_BUFFER_OFFSET(buffer) = num_frames;

// Now, if you want to access the raw data from GStreamer buffer

guint8 *data = info.data;  // Pointer to raw data
gsize data_size = info.size;  // Size of the raw data

// Convert the raw data into OpenCV Mat (assuming RGB format)
cv::Mat frame(height, width, CV_8UC3, data);

// Use the frame (e.g., display or process it)
cv::imshow("Frame", frame);
cv::waitKey(1);

// Remember to unmap the buffer after processing if you haven't already
gst_buffer_unmap(buffer, &info);

```

   - After some enhancements, I wrote this 
```cpp
// I have assumed that teh width and height are 640x480 and 3 channels (RGB)

    int width = 640;
    int height = 480;
    int channels = 3;

    // Calculation of the size of the buffer
    gsize size = width * height * channels;

    //gst_app_src_push_buffer takes ownership of the buffer, so we need to supply it a copy
    GstBuffer *buffer = gst_buffer_new_allocate(NULL, size, NULL);
    GstMapInfo info;
    gst_buffer_map(buffer, &info, GST_MAP_READ);
    GST_BUFFER_DURATION(buffer) = duration;
    GST_BUFFER_PTS(buffer) = timestamp;
    GST_BUFFER_DTS(buffer) = timestamp;
    //set the current number in the frame
    GST_BUFFER_OFFSET(buffer) = num_frames;

    // This is the code to access teh raw data from Gstreamer buffer

    guint8 *data = info.data; //this is the pointer to the raw data
    gsize data_size = info.size; // this is the size of the raw data

    //Conversion of the raw data into OpenCV Mat
    //No memcpy here, directly use the mapped data
    cv::Mat frame(height, width, CV_8UC3, info.data);

    //unmaped the GStreamer buffer  
    gst_buffer_unmap(buffer, &info);
```

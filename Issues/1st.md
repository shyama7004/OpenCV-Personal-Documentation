```cpp
/*!
 * \brief processGStreamerBuffer processes the buffer from GStreamer and integrates it with OpenCV
 * \param buffer Pointer to the GStreamer buffer containing the video frame data
 *
 * This function maps the GStreamer buffer to access its raw data, extracts the video metadata
 * such as width, height, and number of channels, and then converts this data into an OpenCV
 * UMat or Mat for further processing.
 */
void processGStreamerBuffer(GstBuffer *buffer, cl_command_queue queue, cl_mem source_buffer, cl_mem destination_buffer)
{
    GstMapInfo info;

    //to get the video metadata from the GStreamer buffer
    GstVideoMeta *meta = gst_buffer_get_video_meta(buffer);

    if (meta) {
        guint width = meta->width;
        guint height = meta->height;
        guint channels = GST_VIDEO_INFO_N_COMPONENTS(gst_video_meta_get_info(meta));  // Get number of channels from metadata

        //this is a attempt to map the buffer to access raw data
        if (gst_buffer_map(buffer, &info, GST_MAP_READ)) 
        {

            //use  of opencv UMat for handling the  GPU memory
            cv::UMat frame_umat(height, width, CV_8UC3, info.data, cv::USAGE_ALLOCATE_DEVICE_MEMORY);
            cv::Mat frame(height, width, CV_8UC3, info.data);

            // Create OpenCL events and memory transfers
            cl_event event;

            // Non-blocking copy using OpenCL from source_buffer to destination_buffer
            cl_int err = clEnqueueCopyBuffer(queue, source_buffer, destination_buffer, 0, 0, width * height * channels, 0, nullptr, &event);
            if (err != CL_SUCCESS) {
                std::cerr << "Failed to enqueue OpenCL buffer copy. Error: " << err << std::endl;
            }

            clWaitForEvents(1, &event);
            clReleaseEvent(event);

            //unmapping the GStreamer buffer when the task is done
            gst_buffer_unmap(buffer, &info);
        } else {
            std::cerr << "Failed to map GStreamer buffer." << std::endl;
        }
    } else {
        std::cerr << "Failed to get video metadata from buffer." << std::endl;
    }
}

/*!
 * \brief gst_pipeline_process_buffer processes the buffer in the GStreamer pipeline
 * \param buffer Pointer to the GStreamer buffer
 *
 * This function is part of the GStreamer pipeline processing and calls the processGStreamerBuffer
 * function to handle the GStreamer buffer and integrate it with OpenCV.
 */
void gst_pipeline_process_buffer(GstBuffer *buffer, cl_command_queue queue, cl_mem source_buffer, cl_mem destination_buffer)
{
    processGStreamerBuffer(buffer, queue, source_buffer, destination_buffer);
}
```

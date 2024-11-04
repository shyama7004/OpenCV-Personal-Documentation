#### Issue Link : [Click-Here](https://github.com/opencv/opencv/issues/26393)

<details>
<summary>Issue complete details</summary>

### Overview of ML Concepts

Before we dive into the details of the `parseExpandDims` function, let's briefly explain some terms:
- **Tensor**: In ML, a tensor is a multi-dimensional array of numbers. Depending on the number of dimensions, it can be a scalar (0D), vector (1D), matrix (2D), or a higher-dimensional structure (e.g., 3D or 4D).
- **Data Layout**: This refers to how data is organized in a tensor. Two common formats are:
  - **NHWC**: The data is organized as (batch, height, width, channels). Commonly used in TensorFlow.
  - **NCHW**: The data is organized as (batch, channels, height, width). Often used in frameworks like PyTorch and in OpenCV's DNN module.
- **ExpandDims**: An operation that adds a new dimension to a tensor at a specified axis, effectively reshaping the data.

---

### Step-by-Step Explanation of the Code

#### 1. **Function Signature and Initial Setup**
```cpp
void TFImporter::parseExpandDims(tensorflow::GraphDef& net, const tensorflow::NodeDef& layer, LayerParams& layerParams)
```
- **Purpose**: This function is part of the `TFImporter` class. It handles the conversion of a TensorFlow `ExpandDims` operation into a format that OpenCV's DNN module can understand and use.
- **Inputs**:
  - `tensorflow::GraphDef& net`: The entire TensorFlow graph definition, which contains all layers and connections.
  - `const tensorflow::NodeDef& layer`: The current layer being processed.
  - `LayerParams& layerParams`: Stores the parameters for the current layer.

---

### Initial Checks and Preparations

#### 2. **Retrieving Basic Information and Ensuring Inputs Exist**
```cpp
const std::string& name = layer.name(); // Gets the name of the layer.
const int num_inputs = layer.input_size(); // Gets the number of inputs to this layer.

CV_Assert(!netInputShapes.empty()); // Asserts that input shapes are defined.
CV_CheckGT(num_inputs, 0, ""); // Checks that the layer has more than 0 inputs.
```
- **Explanation**:
  - `name`: Stores the name of the current `ExpandDims` layer.
  - `num_inputs`: The number of inputs to the layer. The `ExpandDims` layer should have at least one input.
  - `CV_Assert` and `CV_CheckGT`: These are safety checks to ensure that the input shapes are defined and the layer has inputs. If these conditions aren't met, the program will raise an error.

---

### Parsing the Input

#### 3. **Parsing the First Input and Getting Data Layout**
```cpp
Pin inpId = parsePin(layer.input(0)); // Parses the identifier of the first input.
DataLayout inpLayout = getDataLayout(layer.input(0), data_layouts); // Retrieves the data layout (e.g., NHWC or NCHW).
```
- **Explanation**:
  - `parsePin`: This function extracts the identifier (`Pin`) of the first input tensor to the `ExpandDims` layer.
  - `getDataLayout`: Determines the data layout of the input tensor. The data layout is important because operations might need to be adjusted depending on whether the data is organized as NHWC or NCHW.

#### 4. **Setting Up Shapes for Input and Output**
```cpp
std::vector<MatShape> inShape_, outShape_;
int inpIdindex = layer_id.find(inpId.name)->second;
```
- **Explanation**:
  - `inShape_` and `outShape_`: Vectors to hold the shapes of the input and output tensors.
  - `inpIdindex`: Uses the identifier `inpId` to find the index of the input layer in the `layer_id` map. This index is used to look up shapes later.

---

### Shape Inference with Error Handling

#### 5. **Attempting to Infer Shapes and Handling Errors**
```cpp
try {
    dstNet.getLayerShapes(netInputShapes, inpIdindex, inShape_, outShape_);
} catch (const cv::Exception& e) {
    CV_LOG_WARNING(NULL, "Shape inference failed for layer '" << name << "': " << e.what());
    // Fallback to input shape if shape inference fails
    outShape_ = {inShape_};
}
```
- **Explanation**:
  - **Shape Inference**: The code tries to infer the shapes of the input and output tensors using `dstNet.getLayerShapes`. Shape inference is important for understanding how data flows through the network and ensuring compatibility between layers.
  - **Error Handling**: If shape inference fails, the code catches the exception, logs a warning message, and falls back to using the input shape as the output shape. This ensures the function can proceed even if shape inference isn't perfect.

---

### Working with Input and Output Shapes

#### 6. **Initializing Input and Output Shapes**
```cpp
MatShape inpShape = outShape_[0]; // Uses the inferred shape as the input shape.
std::vector<int> outShape = inpShape; // Initializes the output shape to be the same as the input shape.
int outShapeSize = outShape.size();
```
- **Explanation**:
  - `inpShape`: The shape of the input tensor. This is set to the first element of `outShape_` (inferred shapes).
  - `outShape`: Initializes the output shape to match the input shape. This will later be modified to account for the new dimension added by `ExpandDims`.
  - `outShapeSize`: The size (number of dimensions) of the output shape.

---

### Determining the Axis for Expansion

#### 7. **Handling the Axis Parameter**
```cpp
int axis;
if (layer.input_size() > 1 && value_id.find(layer.input(1)) != value_id.end()) {
    // Axis provided as a constant input
    axis = getConstBlob(layer, value_id, 1).int_val().Get(0);
} else if (layerParams.has("axis")) {
    // Axis provided as an attribute
    axis = layerParams.get<int>("axis");
} else {
    CV_Error(Error::StsBadArg, "ExpandDims layer " + name + " requires axis parameter");
}
```
- **Explanation**:
  - The `axis` specifies where to add the new dimension. For example, in a 3D tensor with shape (2, 3, 4), adding a new dimension at axis 1 would result in (2, 1, 3, 4).
  - The code checks if the axis is provided as a constant input (another tensor) or as an attribute in `layerParams`. If neither is available, it raises an error.

---

### Converting Negative Axis Values

#### 8. **Handling Negative Axis Values**
```cpp
if (axis < 0) {
    axis = inpShape.size() + axis + 1;
}
CV_Assert(0 <= axis && axis <= inpShape.size());
```
- **Explanation**:
  - **Negative Axis Handling**: In TensorFlow and other ML frameworks, negative axis values count from the end. For instance, an axis of -1 would refer to the last dimension. The code converts negative axes to positive values using the formula `inpShape.size() + axis + 1`.
  - **Safety Check**: Ensures the axis is within a valid range.

---

### Layout Conversion Logic

#### 9. **Determining If Layout Conversion Is Needed**
```cpp
bool needsLayoutConversion = false;

// After ExpandDims, 3D data will become 4D data, and OpenCV uses NCHW for 4D data.
if (outShapeSize == 3) {
    if (axis != outShapeSize) {
        needsLayoutConversion = true;
        int order[] = {0, 2, 1};  // Convert NHC (OpenCV's 3D layout) to NCH.
        addPermuteLayer(order, name + "/nch", inpId, 3);
        std::swap(outShape[1], outShape[2]);
    }
    axis = (axis != 0) ? (axis % outShapeSize + 1) : 2;
}
```
- **Explanation**:
  - **3D to 4D Conversion**: If the input is 3D, adding a new dimension makes it 4D. OpenCV uses the NCHW layout for 4D tensors. The code checks if a layout conversion is needed and adds a `PermuteLayer` if so. The `PermuteLayer` rearranges the data order to match NCHW.
  - The `std::swap` function swaps dimensions to adjust the shape correctly.

#### 10. **Handling 4D Data (NHWC to NCHW Conversion)**
```cpp
if (inpShape.size() == 4) {
    if (inpLayout == DNN_LAYOUT_NHWC) {
        static const int nhwc_to_nchw[] = {0, 3, 1, 2};
        if (axis > 0 && axis < 4) {
            axis = nhwc_to_nchw[axis];
        }
    }

    if (axis == inpShape.size() && inpLayout == DNN_LAYOUT_NHWC) {
        needsLayoutConversion = true;
        int order[] =

 {0, 3, 1, 2};  // NHWC to NCHW conversion
        addPermuteLayer(order, name + "/nchw", inpId, inpShape.size());
        std::swap(outShape[1], outShape[3]);
        std::swap(outShape[2], outShape[3]);
    }
}
```
- **Explanation**:
  - For 4D data, the code checks if the layout is NHWC. If so, it adjusts the `axis` value to be compatible with NCHW.
  - If the `axis` is the last dimension and the layout is NHWC, it performs a conversion to NCHW using a `PermuteLayer`.

---

### Final Adjustments and Layer Registration

#### 11. **Adjusting the Output Shape and Registering the Layer**
```cpp
outShape.insert(outShape.begin() + axis, 1);
layerParams.set("shape", DictValue::arrayInt<int*>(&outShape[0], outShape.size()));

if (needsLayoutConversion) {
    layerParams.set("layout", DictValue::get("NCHW"));
}

addLayer(layerParams, inpId, outShape.size(), name, axis);
```
- **Explanation**:
  - **Adding the New Dimension**: The code inserts a new dimension of size 1 at the specified `axis`.
  - **Setting Layer Parameters**: Updates `layerParams` to include the new shape and layout information.
  - **Layer Registration**: Finally, the code registers the `ExpandDims` layer with the updated parameters.

---

### Summary

The `parseExpandDims` function carefully handles the conversion of TensorFlow's `ExpandDims` operation into OpenCV's DNN format. It ensures compatibility between different data layouts (NHWC vs. NCHW) and provides error handling for shape inference. The code is complex because it needs to consider various cases, such as different input shapes and layouts, to work correctly across a range of neural network architectures.</details>

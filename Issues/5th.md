#### Issue Link : [Click-Here](https://github.com/opencv/opencv/issues/26393)

<details>
<summary>Issue complete details</summary>
## This is written by me(everything else in this repo is also written by me :) but this one is raw)

### What parseExpandDims does?
- It's a function that is part of a ML model importer for TensorFlow models in OpenCV's DNN module.
- This function interprets ExpandDims operation from TensorFlow and converts it into a format that we can use in opencv.
- It just adds a new dimension to a tensor, Ex : 3d to 4d or 2d to 3d.

## Code explanation

### Part 1 :

```cpp
Void TFImporter::parseExpandDims(tensorflow::GraphDef& net, const tensorflow::NodeDef& layer, LayerParams& layerParams)
{
    const std::string& name = layer.name();
    const int num_inputs = layer.input_size();

    CV_Assrert(!netInputShapes.empty());
    CV_ChechGT(num_inputs, 0, "");
}
```
### Explanation : 
- The function takes in `net`(the whole TensorFlow graph), `layer`(current layer info), and layerParams(teh parameters for layers).
- It performs initial checks to ensure there are input shapes to work with and that the layer has inputs.

### Part 2 : 

```cpp
Pin inpId = parsePin(layer.input(0));
DataLayout inpLayout = getDataLayout(layer.input(0), data_layouts); // NHWC or NCHW ? 

std::vector<MatShape> inShape_, outShape_;
int inpIndex = layer_id.find(inpId.name)->second;
```
</details>

# Conversion

# Goal

Convert the [tensorflow yolov4 model](https://github.com/hunglc007/tensorflow-yolov4-tflite) using the [tf2onnx converter](https://github.com/onnx/tensorflow-onnx).

# My Steps

## 1. Run tf python script for model evaluation ([detect.py](https://github.com/hunglc007/tensorflow-yolov4-tflite/blob/master/detect.py))

Run `python detect.py --weights ./data/yolov4.weights --framework tf --size 608 --image ./data/kite.jpg`

1. Need to install anaconda and tensorflow!!!! (oops)
    - so I installed anaconda and created a tf environment in that
        - Downloaded anaconda and ran `conda create -n tf tensorflow` → Proceed with `Y` → `conda activate tf`
2. But now it's saying that I don't have cv2, so I gotta install that too
    - `pip install opencv-python`
3. Fingers crossed... nope... it didn't work 😟 can't find module named easy dict
    - so ran `conda install -c conda-forge easydict`
4. Retry... now PIL isn't installed
    - `pip install Pillow==2.2.2` didn't work because microsoft c++ is needed
    - So tried anaconda instead -`conda install -c anaconda pillow` and installation worked 😃
5. retry running that python script for who knows how many times and yay I finally have all the dependencies installed
6. but wait, I haven't downloaded the weights yet oops
7. Weights is now downloaded and placed inside the data folder. Let's see if everything will finally work now
8. and yep! it did!!! YESSS! Progress made!

## 2. Save the tf model in preparation for onnx conversion

Run `python save_model.py --weights ./data/yolov4.weights --output ./checkpoints/yolov4.tf --input_size 416 --model yolov4`

1. Got error — failed to create such a directory for yolov4.tf
    - Changed `/` to `\`, and hopefully that will do it... no more errors? plz?
    Yep! It worked!
2. Model layers recorded [here](./tf-model/yolov4-layers.txt).
2. Visualize it in Netron: looks flat.
3. Input and output shapes recorded [here](./tf-model/yolov4-input-output.jpg).

## 3. Conversion: from tf `.pb` to onnx `.model`

1. Follow installation instructions listed in [tensorflow-onnx repo](https://github.com/onnx/tensorflow-onnx)
    1. Install onnxruntime: `pip install onnxruntime`
    2. Install latest from source `pip install git+https://github.com/onnx/tensorflow-onnx`
    3. Run `tf2onnx.convert` script: `python -m tf2onnx.convert --saved-model ./checkpoints/yolov4.tf --output model.onnx --opset 11 --verbose` 
    4. Tip: hit enter so that the script doesn't get stuck
## 4. Validate the converted model and running it using `onnxruntime`
1. [Netron](./onnx-model/netron-input-output.jpg) input & output visualization
2. Now installing matplotlib to see the finished result
    - `conda install -c conda-forge matplotlib`
3. Also install `jupyter notebook`
    - `conda install -c forge notebook`
4. Install `wget` so that you can get coco photos
    - anaconda installation didn't work
    - it's quite complicated on windows - install the exe and then change the path
    - This [website](https://www.osradar.com/how-to-install-wget-in-windows-10/) really helped
5. Documented my progress in this Jupyter Notebook [here](./onnx-model/tf2onnx-yolov4.ipynb). I think I've made a mistake because the inputs and outputs of my original tf model (and therefore the converted onnx model) do not match with those of the original darknet yolov4 model.
# Known Issues
1. The converted onnx model produced no output when I tried to load it. See this [notebook](./onnx-model/loadOnnxModel.ipynb) for more information.
2. Input and output shapes of the original darknet model and the tf model (from step 1) do not match. However, the tf model and the converted onnx model have the same inputs & outputs. This means that the conversion process went correctly.
    * **Original [darknet yolov4 model](./tf-model/yolov4.cfg.png)** has input shapes of `(512, 512, 3)` and 3 output shapes of  `(64, 64, 255)`, `(32, 32, 255)`, and `(16, 16, 255)`.
    * My **[tf](./tf-model/tf-full.svg)** and **[onnx yolov4](./onnx-model/onnx-full.svg)** models has input shapes of `(-1, 512, 512, 3)` and 3 output shapes of `(-1, -1, -1, 3, 85)`. The "-1" probably means dynamic input.
# Next Steps

1. Try converting from the darknet framework to the onnx framework instead of converting from tensorflow to onnx. It looks like I need to first convert from darknet to PyTorch, then from PyTorch to ONNX, as specified [here](https://github.com/ultralytics/yolov3#darknet-conversion).
2. ALWAYS - visualized in Netron before going onto the next step

# Models
YOLOv4 Tensorflow [model](./tf-model/saved_model.pb)

YOLOv4 converted ONNX [model](./onnx-model/model_old.onnx)

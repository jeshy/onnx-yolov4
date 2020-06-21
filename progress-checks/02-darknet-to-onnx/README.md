# Goal
Convert the [darknet yolov4 model](https://github.com/AlexeyAB/darknet/wiki/YOLOv4-model-zoo) to PyTorch, then from PyTorch to ONNX, following the instruction given [here](https://github.com/ultralytics/yolov3#darknet-conversion).

# My steps
All steps of converting from <u>darknet to pytorch</u> can be found in this [Jupyter notebook](darknet2torch.ipynb). 

I was able to correctly run the converted pytorch model (boxes and classes were correctly rendered).

But I soon found out that there is a way to directly convert from `.cfg` to `.onnx`. So I tried it out in this [notebook](torch2onnx.ipynb). However, when I used used `torch.onnx.export()` to convert, I got the following runtime error. To see the error in more detail, visit [torch2onnx.ipynb](torch2onnx.ipynb)
```
-> 3143         return torch._C._nn.upsample_nearest2d(input, output_size, sfl[0], sfl[1])

Input and output sizes should be greater than 0, but got input (H: 16, W: 16) output (H: 0, W: 0)
```

# Issue
The current question is to figure out what exactly caused the runtime error above. Strangely, when I ran the above conversion code using `yolov3.cfg`, the conversion went through.

# Mistakes I made
1. Do not download the weights using wget. When I used wget, only part of the weights file was downloaded. I spent quite a bit of time trying to figure out why the conversion from darknet to pytorch didn't go through, only to find out that the weight file was wrong.

# Models
PyTorch yolov4 model: [yolov4.pt](yolov4.pt)
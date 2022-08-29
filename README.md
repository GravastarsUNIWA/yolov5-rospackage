# ros_yolov5 ReadMe
Author Kaloterakis Evangelos
## Installation
1. Clone the repository to ```/home/user/catkin_ws/src```
```
cd /home/user/catkin_ws/src
git clone https://github.com/GravastarsUNIWA/ScirocEpisode1.git
cd ScirocEpisode1
git checkout YOLOv5
```
2. 
>Then compile the package and source the workspace
```
cd ..
catkin_make
```

## Install dependencies
### Install anaconda
Yolov5 uses python 3.8. To install go [here](https://tech.serhatteker.com/post/2019-12/upgrade-python38-on-ubuntu/). The installation of the dependencied is done using [anaconda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html). 

### Install package dependencies
1. Create a new virtual environment named venv
```
conda create -n venv python=3.8 jupyter
```
2. Activate new virtual environment
```
conda activate venv
```
3. Install [pytorch](https://pytorch.org/get-started/locally/).
4. Install other dependencies 
```
sudo apt-get install python3-pip python3-yaml
pip3 install -r requirements.txt
pip3 install rospkg catkin_pkg
```

## Choose python interpreter
Modify the shebang at the top of ros_yolov5.py file at ```ros_yolov5/src/ros_yolov5.py```
e.g. Default: ``` #!/usr/bin/env/python```
Modified: ```#!/usr/bin/env/python3``` or ```#!/home/<name of user>/miniconda3/envs/venv/bin/python```

## Usage
To change the weights that Yolov5 uses, open ```ros_yolov5.launch``` on ```ros_yolov5/launch/ros_yolov5.launch``` and change the value of the parameter weights_path to point to your pt file. Moreover change the ```source_topic``` to the correct camera topic.

The YOLOv5 ros package is now ready. Open a terminal and run ```roslaunch ros_yolov5 ros_yolov5.launch```

## Usual errors
### Resize error

!!ATTENTION!!

If you come across this error:

```
[ERROR] [1661788501.044783]: bad callback: <bound method ObjectDetector.image_callback of <__main__.ObjectDetector object at 0x7f011fe2bdc0>>
Traceback (most recent call last):
  File "/opt/ros/melodic/lib/python2.7/dist-packages/rospy/topics.py", line 750, in _invoke_callback
    cb(msg)
  File "/home/user/catkin_ws/src/yolov5-rospackage/ros_yolov5/scripts/ros_yolov5.py", line 46, in image_callback
    results = self.model(img, size=640)
  File "/home/user/miniconda3/envs/venv/lib/python3.8/site-packages/torch/nn/modules/module.py", line 1110, in _call_impl
    return forward_call(*input, **kwargs)
  File "/home/user/miniconda3/envs/venv/lib/python3.8/site-packages/torch/autograd/grad_mode.py", line 27, in decorate_context
    return func(*args, **kwargs)
  File "/home/user/catkin_ws/src/yolov5-rospackage/ros_yolov5/yolov5/models/common.py", line 278, in forward
    y = self.model(x, augment, profile)[0]  # forward
  File "/home/user/miniconda3/envs/venv/lib/python3.8/site-packages/torch/nn/modules/module.py", line 1110, in _call_impl
    return forward_call(*input, **kwargs)
  File "/home/user/catkin_ws/src/yolov5-rospackage/ros_yolov5/yolov5/models/yolo.py", line 122, in forward
    return self.forward_once(x, profile, visualize)  # single-scale inference, train
  File "/home/user/catkin_ws/src/yolov5-rospackage/ros_yolov5/yolov5/models/yolo.py", line 153, in forward_once
    x = m(x)  # run
  File "/home/user/miniconda3/envs/venv/lib/python3.8/site-packages/torch/nn/modules/module.py", line 1110, in _call_impl
    return forward_call(*input, **kwargs)
  File "/home/user/miniconda3/envs/venv/lib/python3.8/site-packages/torch/nn/modules/upsampling.py", line 154, in forward
    recompute_scale_factor=self.recompute_scale_factor)
  File "/home/user/miniconda3/envs/venv/lib/python3.8/site-packages/torch/nn/modules/module.py", line 1185, in __getattr__
    raise AttributeError("'{}' object has no attribute '{}'".format(
AttributeError: 'Upsample' object has no attribute 'recompute_scale_factor'
```
This error is from the internal code of pytorch. The "forward" function is not working properly.

To fix this, replace in ```miniconda3/envs/venv/lib/python3.8/site-packages/torch/nn/modules/upsampling.py```:

```
def forward(self, input: Tensor) -> Tensor:
        return F.interpolate(input, self.size, self.scale_factor, self.mode, self.align_corners,
                         recompute_scale_factor=self.recompute_scale_factor)
```

with:

```
def forward(self, input: Tensor) -> Tensor:
        return F.interpolate(input, self.size, self.scale_factor, self.mode, self.align_corners)
```

Find fix [here](https://github.com/openai/DALL-E/issues/54#issuecomment-1092826376).

For more information see the ReadMe.pdf
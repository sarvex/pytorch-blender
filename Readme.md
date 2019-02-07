# pytorch-blender

Seamless integration of Blender renderings into [PyTorch](http://pytorch.org) datasets for deep learning from artificial visual data. This repository contains a minimal demonstration that harvests images and meta data from ever changing Blender renderings.

Run 

```
python pytorch.py
```

this will render a set of images of random rotated cubes to `./tmp/output_##.png`, such as the following

![](etc/result.png)

This image is generated by 4 Blender instances, randomly perturbating a minimal scene. The results are collated in a `BlenderDataset`. A PyTorch `DataLoader` with batch size 4 is used to grab from the dataset and produce the figure.

## Prerequisites
The following packages need to be available in your PyTorch environment and Blender environment:
 - [Blender](https://www.blender.org/) >= 2.79
 - [PyTorch](http://pytorch.org) >= 0.4, Python >= 3.6
 - [PyZMQ](https://pyzmq.readthedocs.io/en/latest/)
 - [Pillow/PIL](https://pillow.readthedocs.io/en/stable/installation.html)

Both packages are installable via `pip`. In order add packages to your Blender packaged Python distribution, execute the following commands (usually administrator privileges are required on Windows)

```
<BLENDERPATH>2.79\python\bin\python.exe" -m ensurepip
<BLENDERPATH>2.79\python\bin\python.exe" -m pip install pyzmq
<BLENDERPATH>2.79\python\bin\python.exe" -m pip install pillow
```
where `<BLENDERPATH>` is the file path to the directory containing the Blender executable.

## How it works
An instance of `pytorch.py::BlenderLaunch` is responsible for starting and stopping background Blender instances. The script `blender.py` and additional arguments are passed to the starting Blender instance. `blender.py` creates a publisher socket for communication and starts producing random renderings. Meanwhile, a `pytorch.py::BlenderDataset` connects to all publishers registered with `BlenderLaunch` instance. Upon request, the next data bundle is returned.

## Caveats
- In background mode, Blender `ViewerNodes` are not updated, so rendering have to be written to files. Currently, the Blender script re-imports the written image and sends it as message to any subscriber. This way, we do not need to keep track of which files have already been read and can be deleted, which simplifies communication.
- In this sample. only the main composite rendering is transmitted. You might want to use `FileOutputNode` instead, to save multiple images per frame.


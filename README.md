# Soft Rasterizer (SoftRas)

This repository contains the code (in PyTorch) for "[Soft Rasterizer: A Differentiable Renderer for Image-based 3D Reasoning](https://arxiv.org/abs/1904.01786)" (ICCV'2019 Oral) by [Shichen Liu](https://shichenliu.github.io), [Tianye Li](https://sites.google.com/site/tianyefocus/home), [Weikai Chen](http://chenweikai.github.io/) and [Hao Li](https://www.hao-li.com/Hao_Li/Hao_Li_-_about_me.html).

## Contents

1. [Introduction](#introduction)
2. [Usage](#usage)
3. [Applications](#applications)
4. [Contacts](#contacts)

## Introduction

Soft Rasterizer (SoftRas) is a truly differentiable renderer framework with a novel formulation that views rendering as a **differentiable aggregating process** that fuses **probabilistic contributions** of all mesh triangles with respect to the rendered pixels. Thanks to such *"soft"* formulation, our framework is able to (1) directly render colorized mesh using differentiable functions and (2) back-propagate efficient supervision signals to mesh vertices and their attributes (color, normal, etc.) from various forms of image representations, including silhouette, shading and color images. 

<img src="https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/teaser/teaser.png" width="60%">

## Usage

The code is built on Python3 and PyTorch 1.6.0. CUDA (10.1) is needed in order to install the module. Our code is extended on the basis of [this repo](https://github.com/daniilidis-group/neural_renderer). `6/3/2021` update note: we add **testing models** and **recontructed color meshes** below, and also slightly optimized the code structure! Previous version is archived in the `legacy` branch.


To install the module, using

```
python setup.py install
```

## Applications

### 0. Rendering

We demonstrate the rendering effects provided by SoftRas. Realistic rendering results (1st and 2nd columns) can be achieved with a proper setting of `sigma` and `gamma`. With larger `sigma` and `gamma`, one can obtain renderings with stronger transparency and blurriness (3rd and 4th column).

![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/render/forward.gif)

```
CUDA_VISIBLE_DEVICES=0 python examples/demo_render.py
```

### 1. 3D Unsupervised Single-view Mesh Reconstruction

By incorporating SoftRas with a simple mesh generator, one can train the network with multi-view images only, without requiring any 3D supervision. At test time, one can reconstruct the 3D mesh, along with the mesh texture, from a single RGB image. Below we show the results of single-view mesh reconstruction on ShapeNet.

![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/recon_rgb/02691156_150_000867_w_input.gif) ![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/recon_rgb/02958343_150_001155_w_input.gif) ![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/recon_rgb/03001627_150_002475_w_input.gif) ![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/recon_rgb/04090263_150_004755_w_input.gif) ![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/recon_rgb/04256520_150_004035_w_input.gif) ![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/recon_rgb/04379243_150_000843_w_input.gif)

Download shapenet rendering dataset provided by NMR:
```
bash examples/recon/download_dataset.sh
```

To train the model:
```
CUDA_VISIBLE_DEVICES=0 python examples/recon/train.py -eid recon
```

To test the model:
```
CUDA_VISIBLE_DEVICES=0 python examples/recon/test.py -eid recon \
    -d 'data/results/models/recon/checkpoint_0200000.pth.tar'
```

We also provide our trained model here:
- SoftRas trained with silhouettes supervision (62+ IoU): [google drive](https://drive.google.com/file/d/1GlZJVih5BMGp026mpxK2scWJXqT94VUx/view?usp=sharing)
- SoftRas trained with shading supervision (64+ IoU, test with `--shading-model` arg): [google drive](https://drive.google.com/file/d/1r63AKNn3ecMho6RFE7gFefRv78Pmbe5h/view?usp=sharing)
- SoftRas reconstructed meshes with color (random sampled): [google drive](https://drive.google.com/file/d/1gnSshn0k9JpVpoSTWIQoV2QFAlin3AUK/view?usp=sharing)

### 2. Image-based Shape Deformation

SoftRas provides strong supervision for image-based mesh deformation. We visualize the deformation process from a sphere to a car model and then to a plane given supervision from multi-view silhouette images.

![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/deform/sphere_to_car.gif) ![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/deform/car_to_plane.gif)

```
CUDA_VISIBLE_DEVICES=0 python examples/demo_deform.py
```

The optimized mesh is included in `data/obj/plane/plane.obj`

### 3. Pose Optimization for Rigid Objects

With scheduled blurry renderings, one can obtain smooth energy landscape that avoids local minima. 
Below we demonstrate how a color cube is fitted to the target image in the presence of large occlusions.
The blurry rendering and the corresponding rendering losses are shown in the 3rd and 4th columns respectively.

![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/fitting/cubic.gif)

### 4. Non-rigid Shape Fitting

We fit the parametric body model (SMPL) to a target image where the part (right hand) is entirely occluded in the input view.

![](https://raw.githubusercontent.com/ShichenLiu/SoftRas/master/data/media/demo/fitting/smpl.gif)

## Contacts
Shichen Liu: <liushichen95@gmail.com>

Any discussions or concerns are welcomed!

### Citation

If you find our project useful in your research, please consider citing:

```
@article{liu2019softras,
  title={Soft Rasterizer: A Differentiable Renderer for Image-based 3D Reasoning},
  author={Liu, Shichen and Li, Tianye and Chen, Weikai and Li, Hao},
  journal={The IEEE International Conference on Computer Vision (ICCV)},
  month = {Oct},
  year={2019}
}
```

```
@article{liu2020general,
  title={A General Differentiable Mesh Renderer for Image-based 3D Reasoning},
  author={Liu, Shichen and Li, Tianye and Chen, Weikai and Li, Hao},
  journal={IEEE Transactions on Pattern Analysis and Machine Intelligence},
  year={2020},
  publisher={IEEE}
}
```

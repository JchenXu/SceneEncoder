# SceneEncoder: Scene-Aware Semantic Segmentation of Point Clouds with A Learnable Scene Descriptor 
by Jiachen Xu*, Jingyu Gong*, Jie Zhou, Xin Tan, Yuan Xie and Lizhuang Ma. (*=equal contribution)

<p align="center"> <img src="imgs/framework.png" width="80%"> </p>

## Introduction
This project is based on our IJCAI2020 paper. You can find the [arXiv](https://arxiv.org/abs/2001.09087) version here.
```
@article{xu2020sceneencoder,
  title={SceneEncoder: Scene-Aware Semantic Segmentation of Point Clouds with A Learnable Scene Descriptor},
  author={Xu, Jiachen and Gong, Jingyu and Zhou, Jie and Tan, Xin and Xie, Yuan and Ma, Lizhuang},
  journal={arXiv preprint arXiv:2001.09087},
  year={2020}
}
```
Besides local features, global information plays an essential role in semantic segmentation, while recent works usually fail to explicitly extract the meaningful global information and make full use of it. In this paper, we propose a SceneEncoder module to impose a scene-aware guidance to enhance the effect of global information. The module predicts a scene descriptor, which learns to represent the categories of objects existing in the scene and directly guides the point-level semantic segmentation through filtering out categories not belonging to this scene. Additionally, to alleviate segmentation noise in local region, we design a region similarity loss to propagate distinguishing features to their own neighboring points with the same label, leading to the enhancement of the distinguishing ability of point-wise features. We integrate our methods into several prevailing networks and conduct extensive experiments on benchmark datasets ScanNet and ShapeNet. Results show that our methods greatly improve the performance of baselines and achieve state-of-the-art performance.

## Installation
The code is based on [PointNet](https://github.com/charlesq34/pointnet), [PointNet++](https://github.com/charlesq34/pointnet2), [SpiderCNN](https://github.com/xyf513/SpiderCNN) and [PointConv](https://github.com/DylanWusee/pointconv). Please install [TensorFlow](https://www.tensorflow.org/install/), and follow the instruction in [PointNet++](https://github.com/charlesq34/pointnet2) to compile the customized TF operators in the *tf_ops* directory. Specifically, you may need to check tf_xxx_compile.sh under each *tf_ops* subfolder, and modify ${CUDA_PATH} and the version of python if necessary.

For example, you need to change the shell script *tf_ops/3d_interpolation/tf_interpolate_compile.sh* from
```
CUDA_PATH=/usr/local/cuda
```
to 
```
CUDA_PATH=/usr/local/cuda-9.0
```
if you use CUDA 9.0.

Additionally, you need to specifically change the command
```
TF_INC=$(python -c 'import tensorflow as tf; print(tf.sysconfig.get_include())')
```
to 
```
TF_INC=$(python3 -c 'import tensorflow as tf; print(tf.sysconfig.get_include())')
```
if your default python is python2.

The code has been tested with Python 3.6, TensorFlow 1.13.1, CUDA 10.0 and cuDNN 7.3 on Ubuntu 18.04.

To install some of required package, run:
```
pip install -r requirements.txt
```

## Usage
### ScanNet DataSet Segmentation
Please download the ScanNetv2 dataset from [here](http://www.scan-net.org/), and see `scannet/README` for details of preprocessing.

To train a model to segment Scannet Scenes:

```
CUDA_VISIBLE_DEVICES=0 python train_scannet_IoU.py --model scene_encoder_rsl --log_dir scannet_ --batch_size 8
```

After training, to generate test results to *dump_%s* directory:

```
CUDA_VISIBLE_DEVICES=0 python evaluate_scannet.py --model scene_encoder_rsl --batch_size 8 --model_path scannet_%s
```

Then, to upload the results to the ScanNetv2 benchmark server:
```
zip out.zip dump_%s/scene*
```

(Optional) To visualize the results on validation dataset:
```
CUDA_VISIBLE_DEVICES=0 python visualize_scene.py --model scene_encoder_rsl --batch_size 8 --model_path scannet_%s --ply_path DataSet/ScanNetv2/scans
```

Modify the model_path to your .ckpt file path and the ply_path to the original [ScanNetv2](http://www.scan-net.org/) ply file.

### ShapeNet DataSet Segmentation

Please download the ShapeNet Part dataset from [here](https://shapenet.cs.stanford.edu/media/shapenetcore_partanno_segmentation_benchmark_v0_normal.zip) and put it under the data directory. Unzip it under the corresponding directory:
```
unzip shapenetcore_partanno_segmentation_benchmark_v0_normal.zip
```
To train a model to finish the part segmentation and evaluate it:
```
CUDA_VISIBLE_DEVICES=0 python train_shapenet_IoU.py
```
(Optional) To visualize the results on ShapeNet Part dataset:
```
CUDA_VISIBLE_DEVICES=0 python visualize_part.py --model_path log_shapenet_%s
```
Under testing :=)

## Acknowledgement
Thanks:

[Cardinal2376](https://github.com/Cardinal2376) for advices on requirements.txt and instructions of installation

## License
This repository is released under MIT License (see LICENSE file for details).

---
layout: post
title: ubuntu18.04+gt1066+cuda+cudnn+tensorflow+keras环境搭建
date:   2018-11-15 00:00:00 +0800
categories: document
tag: 教程
---

* content
{:toc}


## ubuntu18.04安装
u盘安装，很简单  
## gt1066驱动安装
1. 禁用nouveau驱动
```shell
$ sudo mv /lib/modules/4.15.0-39-generic/kernel/drivers/gpu/drm/nouveau/nouveau.ko /lib/modules/4.15.0-39-generic/kernel/drivers/gpu/drm/nouveau/nouveau.ko.bak
$ sudo update-initramfs -u
$ sudo reboot
```
重启发现字体变大了，卸载成功  
2. 安装nvidia驱动
```shell
$ sudo ubuntu-drivers autoinstall
$ sudo reboot
```
重启生效  
	```shell  
	$ nvidia-smi 
	Mon Nov 12 22:13:51 2018       
	+-----------------------------------------------------------------------------+
	| NVIDIA-SMI 410.48                 Driver Version: 410.48                    |
	|-------------------------------+----------------------+----------------------+
	| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
	| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
	|===============================+======================+======================|
	|   0  GeForce GTX 106...  Off  | 00000000:01:00.0  On |                  N/A |
	| 37%   27C    P8    10W / 120W |    278MiB /  6076MiB |      0%      Default |
	+-------------------------------+----------------------+----------------------+
																				   
	+-----------------------------------------------------------------------------+
	| Processes:                                                       GPU Memory |
	|  GPU       PID   Type   Process name                             Usage      |
	|=============================================================================|
	|    0       897      G   /usr/lib/xorg/Xorg                           139MiB |
	|    0      1044      G   /usr/bin/gnome-shell                          78MiB |
	|    0      6368      G   ...quest-channel-token=9525180073602140728    58MiB |
	+-----------------------------------------------------------------------------+
	```
## 安装cuda10
在官网下载cuda安装包，按网站上的说明安装即可，不用替换什么文件  
![png]({{ site.baseurl }}/assets/img/cuda1.png)  
```shell
$ sudo dpkg -i cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64.deb
$ sudo apt-key add /var/cuda-repo-<version>/7fa2af80.pub
$ sudo apt-get update
$ sudo apt-get install cuda
```
## 安装cudnn7.4.1
在官网注册，下载cudnn安装包，dpkg安装即可  
![png]({{ site.baseurl }}/assets/img/cudnn1.png)  
安装后添加环境变量  
```shell
$ sudo dpkg -i libcudnn7_7.4.1.5-1+cuda10.0_amd64.deb
```
```shell
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
export PATH="$CUDA_HOME/bin:$PATH"
```
## 安装tensorflow
pip安装tensorflow结果tensorflow只支持cuda9.0，但cuda9.0对应的cudnn不支持ubuntu18.04  
从网上找到办法用anaconda/miniconda来安装tensorflow不会出现问题  
从清华开源镜像下载miniconda，并安装: [](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/)  
直接下载最新的"Miniconda3-latest-Linux-x86_64.sh"安装，并设置环境变量  
```shell
export PATH="/home/lmzl/miniconda3/bin:$PATH"
```
```shell
$ sudo conda install tensorflow
```
进入python import下成功，说明安装成功，也可以用conda list查看  
```shell
$ conda list
# packages in environment at /home/lmzl/miniconda3:
#
# Name                    Version                   Build  Channel
...
tensorflow-gpu            1.5.0                         0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
tensorflow-gpu-base       1.5.0            py36h8a131e3_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
tensorflow-tensorboard    1.5.1            py36hf484d3e_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
...
```
## 安装keras
这里就随意了，`pip install keras`或着`conda install keras`  
试用下cudnn效果  
```shell
$ git clone git@github.com:keras-team/keras.git
$ cd keras
$ python examples/mnist_cnn.py 
Using TensorFlow backend.
x_train shape: (60000, 28, 28, 1)
60000 train samples
10000 test samples
Train on 60000 samples, validate on 10000 samples
Epoch 1/12
2018-11-12 22:49:49.546799: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-11-12 22:49:49.666604: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:895] successful NUMA node read from SysFS had negative value (-1), but there must be at least one NUMA node, so returning NUMA node zero
2018-11-12 22:49:49.666897: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1105] Found device 0 with properties: 
name: GeForce GTX 1060 6GB major: 6 minor: 1 memoryClockRate(GHz): 1.7085
pciBusID: 0000:01:00.0
totalMemory: 5.93GiB freeMemory: 5.59GiB
2018-11-12 22:49:49.666917: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1195] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: GeForce GTX 1060 6GB, pci bus id: 0000:01:00.0, compute capability: 6.1)
60000/60000 [==============================] - 12s 193us/step - loss: 0.2736 - acc: 0.9165 - val_loss: 0.0589 - val_acc: 0.9811
Epoch 2/12
60000/60000 [==============================] - 8s 138us/step - loss: 0.0889 - acc: 0.9740 - val_loss: 0.0375 - val_acc: 0.9874
Epoch 3/12
60000/60000 [==============================] - 8s 140us/step - loss: 0.0655 - acc: 0.9805 - val_loss: 0.0343 - val_acc: 0.9877
Epoch 4/12
60000/60000 [==============================] - 8s 138us/step - loss: 0.0537 - acc: 0.9842 - val_loss: 0.0314 - val_acc: 0.9890
Epoch 5/12
60000/60000 [==============================] - 8s 136us/step - loss: 0.0456 - acc: 0.9861 - val_loss: 0.0343 - val_acc: 0.9892
Epoch 6/12
60000/60000 [==============================] - 8s 138us/step - loss: 0.0412 - acc: 0.9871 - val_loss: 0.0299 - val_acc: 0.9900
Epoch 7/12
60000/60000 [==============================] - 8s 140us/step - loss: 0.0372 - acc: 0.9885 - val_loss: 0.0247 - val_acc: 0.9910
Epoch 8/12
60000/60000 [==============================] - 8s 134us/step - loss: 0.0343 - acc: 0.9893 - val_loss: 0.0260 - val_acc: 0.9912
Epoch 9/12
60000/60000 [==============================] - 8s 137us/step - loss: 0.0327 - acc: 0.9898 - val_loss: 0.0250 - val_acc: 0.9914
Epoch 10/12
60000/60000 [==============================] - 8s 137us/step - loss: 0.0308 - acc: 0.9907 - val_loss: 0.0263 - val_acc: 0.9928
Epoch 11/12
60000/60000 [==============================] - 8s 137us/step - loss: 0.0279 - acc: 0.9914 - val_loss: 0.0286 - val_acc: 0.9919
Epoch 12/12
60000/60000 [==============================] - 8s 139us/step - loss: 0.0277 - acc: 0.9920 - val_loss: 0.0277 - val_acc: 0.9923
Test loss: 0.027729490233584647
Test accuracy: 0.9923
```
如果每一步时间都很长，比如要100+s那就是没生效，可以重启下再试试  
查看gpu使用情况
```shell
$ watch -n 3 nvidia-smi
```

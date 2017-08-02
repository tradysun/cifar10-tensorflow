# 图像分类中的图像显著性区域

## 一、问题描述

图像分类问题中，检测图像显著性区域，图像显著性区域指的是，图像中的这些区域对于图像分类的权重最高，例如，如果要识别下图中的青蛙，那么很显然，图像中的前景部分，即紫色的青蛙是图像中的显著性区域，是对于该图片分类到“青蛙”类别的权重最高。而图像的背景部分，即蓝色的河水则不是显著性区域。换句话说，图像分类中的显著性区域检测就是要寻找图像中对于分类任务权重大的区域。图像分类示例图像如图1。

![图像分类示例图像](/others/pictures/frog.png)

图1、图像分类示例图像

本文使用的数据集是CIFAR-10数据集，该数据集有50000张图片，每张图片均为分辨率为32*32的彩色图片（分为3个信道）。分类任务需要分成青蛙、卡车、飞机等10个类别。本文设计一种卷积神经网络用于处理图像分类任务，接下来首先介绍基于卷积神经网络的分类模型。为了训练该网络，首先将数据分为训练集、验证集和测试集3个数据集，训练集样本个数为45000，验证集样本个数为5000，测试集样本个数为10000。

## 二、分类模型

### 模型1——基本卷积网络

模型的输入数据是网络的输入是一个4维tensor，尺寸为(128, 32, 32, 3)，分别表示一批图片的个数128、图片的宽的像素点个数32、高的像素点个数32和信道个数3。首先使用多个卷积神经网络层进行图像的特征提取，卷积神经网络层的计算过程如下步骤：

1. 卷积层1：卷积核大小3\*3，卷积核移动步长1，卷积核个数64，池化大小2\*2，池化步长2，池化类型为最大池化，激活函数ReLU。
2. 卷积层2：卷积核大小3\*3，卷积核移动步长1，卷积核个数128，池化大小2\*2，池化步长2，池化类型为最大池化，激活函数ReLU。
3. 卷积层3：卷积核大小3\*3，卷积核移动步长1，卷积核个数256，池化大小2\*2，池化步长2，池化类型为最大池化，激活函数ReLU。
4. 全连接层：隐藏层单元数1024，激活函数ReLU。
5. 分类层：隐藏层单元数10，激活函数softmax。

参数初始化：所有权重向量使用random_normal(0.0, 0.001)，所有偏置向量使用constant(0.0)，

使用cross entropy作为目标函数，使用Adam梯度下降法进行参数更新，学习率设为固定值0.001。

训练5000轮，观察到loss变化曲线、训练集准确率变化曲线和验证集准确率变化曲线如图2。测试集准确率为0.6936。

![model1](/results/cifar10-v1/cifar10-v1.png)

图2、模型1的loss收敛曲线图和验证集准确率图

### 模型2——数据增强（Data Augmentation）

在模型1的基础上，加强数据预处理部分，称为数据增强（data augmentation）技术。使用数据增强技术，主要在训练数据上增加微小的扰动或者变化，一方面可以增加训练数据，从而提升模型的性能，另一方面可以增加噪声数据，从而增强模型的鲁棒性。cifar10数据集主要做的数据增强操作有如下方面：

1.  图像切割：生成比图像尺寸小一些的矩形框，对图像进行随机的切割，最终以矩形框内的图像作为训练数据。
2.  图像翻转：对图像进行左右翻转。
3.  图像白化：对图像进行白化操作，即将图像本身归一化成Gaussian(0,1)分布。

对于训练集，分别进行1~3个步骤的数据增强，对于验证集和测试集，只进行1和3步骤。训练5000轮，观察到loss变化曲线、训练集准确率变化曲线和验证集准确率变化曲线如图3。测试集准确率为。

![model1](/results/cifar10-v2/cifar10-v2.png)

图3、模型2的loss收敛曲线图和验证集准确率图

---
title: 逐渐增长的GAN
tags: gan
date: 2017-11-01 00:00:36
---


## 概要
[文献地址](http://research.nvidia.com/sites/default/files/pubs/2017-10_Progressive-Growing-of//karras2017gan-paper.pdf)
[代码](https://github.com/tkarras/progressive_growing_of_gans)

本文的中心思想是让generator和discrimator逐渐地变复杂，一开始是生成和处理低像素的图像，然后逐渐加入新的层，引入更高的像素。

文章贡献：
1. 新方法使得训练更加稳定，训练时间更短
2. 生成的高像素图像十分逼真（1024*1024）
3. 文章提出了增加多样性的方法。 
4. 提出了一种新的衡量图片质量和多样性的metrics。

## 逐步生长的训练方法
在训练模型的时候，一开始只有4 \* 4的一层网络，逐渐加到1024 \* 1024。借此可以先发现图像空间的大尺度结构，然后逐渐关注细节，而不是同时学习所有的尺度。

{% asset_img training_progress.png %}

在加入新的大尺度网络层时，文章提出要渐渐加入，而不是一次性放上去。可以通过`(1-alpha)*A + alpha*B`计算，其中A是已经训练好的小尺度，B是要新加入的大尺度网络。然后逐渐增加alpha的值。

{% asset_img fade_in.png %}


高像素图像产生的难点是因为像素越高，discrimitor可以用来区分的数据就越多，更加容易区分。而且高像素图像会需要小的minibatch，这是硬件的限制导致的。

## 使用minibatch的标准差来增加多样性
借鉴了[这篇文章](https://arxiv.org/abs/1606.03498)的minibatch discrimination的方法
TODO: 读一下这篇文章。

## normalization的一些手段
### equalized learning rate
### Pixelwise feature vector normalization in generator

## 新的gan结果衡量打分方法
思路是好的generator生成的图像在各个尺度上都应该和训练图像的结构相近。这个要怎么衡量呢？作者提出要计算各尺度的统计上的相似度。而计算的数据来自生成图像和原始图像在Laplacian pyramid上采样得到的image patch。

什么是Laplacian pyramid呢？
代表的是图像的空间频段信息，每一层都是一个频率。从16 \* 16一直到1024 \* 1024. TODO：理解拉普拉斯金字塔

具体做法：TODO


We will describe our method for encouraging variation in Section 3, and propose a new metric for evaluating the quality and variation in Section 5.

Use statistics between minibatches to avoid mode collapse

Propose a method to avoid mode collapse

Our primary contribution is a training methodology for GANs where we start with low-resolution images, and then progressively increase the resolution by adding layers to the networks



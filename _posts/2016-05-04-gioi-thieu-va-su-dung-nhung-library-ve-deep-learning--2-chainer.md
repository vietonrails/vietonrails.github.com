---
layout: post
title: "Giới thiệu và sử dụng những library về Deep Learning : 2. Chainer"
description: ""
category: AI
tags: [deep learning, chainer]
comments: true
author: rockkhuya
---
* TOC
{:toc}

Chainer được công ty Preferred Networks opensource từ tháng 6/2015.

[Chainer](http://chainer.org/)

Công ty Preferred networks là 1 công ty rất nổi tiếng trong lĩnh vực AI và bigdata của Nhật. Công ty này cũng tham gia dự án Jubatus - dự án machine learning cho mục đích online. Công ty này gồm toàn "siêu nhân".

<!-- more -->

Các đặc trưng của Chainer :

* library của Python
*  thích hợp với các mô hình neural
*  support GPU và có thể sử dụng nhiều GPU cùng lúc để training.
*  Code rất trực quan bằng cách graph quá trình tính toán.

## so sánh với tensorflow

Nhìn vào các đặc trưng thì tensorflow và chainer khá giống nhau. Với bài test MNIST thì kết quả cũng ngang ngửa nhau. 

Tuy nhiên tensorflow hỗ trợ cho muti GPU tốt hơn. Chainer thì lại có tốc độ training và predict nhanh hơn. 

## install

Vì chainer là library của Python nên cách install là vô cùng đơn giản. 

```bash 
$ pip install chainer
```

Xong.

## test với MNIST

Clone từ github về và chạy đoạn script có sẵn trong đó. 

```bash
$ git clone https://github.com/pfnet/chainer.git
$ python chainer/examples/mnist/train_mnist.py
```

Quá trình download MNIST, training, test đều được làm tự động. 

```bash
load MNIST dataset
Downloading train-images-idx3-ubyte.gz...
...
epoch 1
graph generated
train mean loss=0.190947790003, accuracy=0.942850003242
test  mean loss=0.0990746175707, accuracy=0.96930000484
...
epoch 20
train mean loss=0.0104963570454, accuracy=0.996966669559
test  mean loss=0.102703116325, accuracy=0.982000006437
```

Kết quả cuối cùng đạt 98%. 

---
layout: post
title: "Giới thiệu và sử dụng những library về Deep Learning : 4. DeepDream"
description: ""
category: AI
tags: [deep learning, DeepDream]
comments: true
author: rockkhuya
---
DeepDream là 1 OSS được Google open vào tháng 7/2015 ( trước TensorFlow ). 

> Từ 1 bức ảnh, DeepDream sẽ tìm ra những vật - những hình ảnh mà nó biết, sau đó sẽ tái cấu trúc lại bức ảnh. 
> Neural Network được tạo thành từ 10~30 layers, layer lớp đầu tiên sẽ input thông tin của bức ảnh, tìm ra các góc - cạnh trong bức ảnh. Layer lớp thứ 2, thứ 3 và các layer tiếp theo sẽ lấy thông tin của các vật thể cơ bản, layer cuối cùng sẽ tổ hợp các thông tin đó lại và tạo ra 1 "vật" gì đó. 

Một vài ví dụ về `DeepDream`:

![pic 1](http://www.ucreative.com/wp-content/uploads/2015/07/4ZSWD4L-e1436336191130.jpg)

![pic2](http://i.telegraph.co.uk/multimedia/archive/03370/doge_3370416k.jpg)

![pic4](http://i.telegraph.co.uk/multimedia/archive/03370/magic_3370394k.jpg)

<!-- more -->


## Configure

`Caffe` là bắt buộc, và chỉ cần `Caffe` là có thể nghịch được 1 số thứ rồi. 

### iPython notebook

```bash
$ pip install ipython
$ pip install 'ipython[notebook]'
```

### clone DeepDream

clone DeepDream về thư mục chứa `Caffe`. 

```bash
$ git clone git@github.com:google/deepdream.git
```

### download model

Download model đã được training xong của `Caffe` [ở đây](http://dl.caffe.berkeleyvision.org/bvlc_googlenet.caffemodel)

Save trong thư mục : `{caffe PATH}/models/bvlc_googlenet/ `

## Test

Tới thư mục deepdream vừa clone về và khởi động iPython notebook.

```bash
$ cd deepdream
$ ipython trust dream.ipynb
$ ipython notebook
```

Click liên tục vào nút "run cell" trong phần menu. 

ipython và notebook tạo ra những bài giảng mà bạn phải chạy từng phần, từng dòng và đi kèm với đó là hướng dẫn và giải thích. 

## DeepDream bằng tensorflow

Dù `Tensorflow` ra đời muộn hơn `caffe` khá nhiều, nhưng với cộng đồng rất mạnh, họ cũng đưa ra được 1 tutorial dùng `tensorflow` với thuật toán của DeepLearning.

Bạn có thể tham khảo ở đây :

[tensorflow tutorial](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/examples/tutorials/deepdream)

### Kết quả

Sử dụng DeepDream vào 1 vài bức ảnh thì mình có kết quả thế này.

![DeepDream-caferacer](/public/images/DeepDream-caferacer.jpg)

Ví dụ ác mộng với nền là những chú chó.

![DeepDream-caferacer-dog](/public/images/DeepDream-caferacer-dog.jpeg)

Ví dụ với nền là những bông hoa.

![DeepDream-caferacer-flow](/public/images/DeepDream-caferacer-flow.jpeg)

---
layout: post
title: "Giới thiệu và sử dụng những library về Deep Learning : 1. TensorFlow"
description: ""
category: AI
tags: [deep learning, tensorflow]
comments: true
author: rockkhuya
---
* TOC
{:toc}

Bài viết này giới thiệu và hướng dẫn cách install và sử dụng các library nổi tiếng về DeepLearning hiện tại như TensorFlow, Chainer, Caffe, DeepDream.
Bài viết này không đi sâu về khái niệm và giải thích bản chất thuật toán của vấn đề, đơn giản là cách sử dụng và 1 phần nào đó là khả năng của Deep Learning.

Trong bài viết này ta sẽ thử 3 library/framework đề machine learning và deep learning nổi tiếng nhất hiện nay, đó là TensorFlow, Chainer, Caffe. Cùng với đó là 2 app nổi tiếng trong năm 2015 dùng deep learning là DeepDream và chainer-gogh.

Môi trường mình đang làm việc :

1. Mac OS X 10.11.1 El Capitan
2. Xcode 7.2

<!-- more -->

## TensorFlow

TensorFlow là 1 library do Google phát triển và opensource vào tháng 11/2015. TensorFlow được cho là sử dụng trong nhiều service của Google.

### Vài nét về TensorFlow

1. Sử dụng trong rất nhiều ứng dụng của Google như phần loại email của Gmail, nhận biết phát âm và dịch tự động, nhận biết khuôn mặt trong Google Photo, tối ưu hoá kết quả tìm kiếm, quảng cáo trong Youtube, ...
2. Đặc trưng của TensorFlow là xử lý được tất cả các loại dữ liệu có thể biểu diễn dưới dạng data flow graph hay low level như xử lý chữ viết tay. TensorFlow được viết bằng C++, thao tác interface bằng Python vì thế performance rất tốt, dùng được cả CPU lẫn GPU nên TF có thể chạy trên cả PC thông thường lẫn 1 server cực lớn, thậm chí cả smartphone cũng có thể sử dụng được.

### Vì sao Google opensource TF ?

License của TF là Apache 2.0, tức là có thể sử dụng trong các sản phẩm thương mại khác. Tại sao 1 công cụ tuyệt vời được sử dụng trong hầu hết các sản phẩm của Google lại được opensource 1 cách tự do như thế ? Và đây là lời giải thích của Google :

> Bằng việc opensource TF, Google muốn khuyến khích nhiều người sử dụng hơn nữa, từ sinh viên, giáo viên cho đến các developer có hứng thú với AI. Bằng cách này, TF nói riêng hay AI nói chung sẽ được sử dụng trong nhiều lĩnh vực hơn nữa, từ y học cho đến khoa học vũ trụ. Tất cả những lợi ích đó đều quay lại phục vụ CON NGƯỜI.

Đó là lý do mà Google đã, đang và sẽ luôn đi đầu trong giới công nghệ.

### Configure

[Hướng dẫn install tensorflow của google](https://www.tensorflow.org/versions/master/get_started/os_setup.html)

Yêu cầu là phải có Python 2.7 hoặc Python 3.3+.

#### Install pip

`pip` là 1 cách install các library rất thuận tiện của Python.

```bash
$ sudo easy_install pip
```

#### Install virtualenv

`virtualenv` là tool quản lý version của python giống rvm.

```bash
$ sudo pip install --upgrade virtualenv
```

#### Tạo môi trường virtualenv

```bash
$ virtualenv --system-site-packages ~/tensorflow
$ source ~/tensorflow/bin/activate
```

Đến đây thì màn hình command sẽ thay đổi.

#### Install TF

```bash
(tensorflow)$ pip install --upgrade https://storage.googleapis.com/tensorflow/mac/tensorflow-0.5.0-py2-none-any.whl
```

Kết quả như sau là OK.

```
Successfully installed numpy-1.10.2 six-1.10.0 tensorflow-0.5.0
```

### Hello World

Tạo 1 file tên `hello_world.py` trong thư mục `tensorflow` trên và code nội dung như sau :

```python
import tensorflow as tf
import multiprocessing as mp

core_num = mp.cpu_count()
config = tf.ConfigProto(
    inter_op_parallelism_threads=core_num,
    intra_op_parallelism_threads=core_num )
sess = tf.Session(config=config)

hello = tf.constant('hello, tensorflow!')
print sess.run(hello)
```

Run trong môi trường TF.

```bash
(tensorflow)$ python hello_world.py
```

Kết quả :

```text
I tensorflow/core/common_runtime/local_device.cc:25] Local device intra op parallelism threads: 4
I tensorflow/core/common_runtime/local_session.cc:45] Local session inter op parallelism threads: 4
hello, tensorflow!
```

### Tutorial : MNIST for ML Beginners

Đây là bài tutorial cơ bản của TensorFlow. Trong bài tutorial TF sẽ được sử dụng để học và nhận dạng chữ viết tay.

[MNIST For ML Beginners](https://www.tensorflow.org/versions/master/tutorials/mnist/beginners/index.html)

Mình khuyến khích các bạn nên đọc bài tutorial của TF. Các bạn nào muốn đi tắt thì mình có thể clone code của tutorial từ Github và run.

```bash
$ git clone --recurse-submodules https://github.com/tensorflow/tensorflow
```

Sửa dòng 30, 31 trong `fully_connected_feed.py`

Trước :

```python
from tensorflow.examples.tutorials.mnist import input_data
from tensorflow.examples.tutorials.mnist import mnist
```

Sửa thành :

```python
import input_data
import mnist
```

Run :

```bash
$ cd tensorflow/
$ python tensorflow/examples/tutorials/mnist/fully_connected_feed.py
```

Bằng cách này ta không cần phải download MNIST dataset bằng tay, đoạn script sẽ làm toàn bộ từ download cho đến training.

Kết quả nhận được :

```bash
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting data/train-images-idx3-ubyte.gz
//中略
Step 0: loss = 2.32 (0.025 sec)
Step 100: loss = 2.19 (0.003 sec)
Step 200: loss = 1.97 (0.003 sec)
//中略
Step 1900: loss = 0.46 (0.004 sec)
Training Data Eval:
  Num examples: 55000  Num correct: 49489  Precision @ 1: 0.8998
Validation Data Eval:
  Num examples: 5000  Num correct: 4534  Precision @ 1: 0.9068
Test Data Eval:
  Num examples: 10000  Num correct: 9019  Precision @ 1: 0.9019
```

Độ chính xác của cách training này là khoảng 90%. Bằng 1 vài thay đổi bạn có thể nâng độ chính xác lên 99,8% (!). Mình sẽ phân tích trong 1 bài viết khác cụ thể hơn.

### sử dụng TensorBoard

Tiếp theo là 1 tính năng rất thú vị khác của TF. Đó là hiển thị kết quả training bằng graph.

```bash
$ tensorboard --logdir=/Users/xxxx/xxxx/tensorflow/tensorflow/data
```

Path ở đây là path tuyệt đối  dẫn đến thư mục chứa log của quá trình training.

```
Starting TensorBoard on port 6006
(You can navigate to http://localhost:6006)
```

Bằng cách vào đường dẫn localhost đó ta có thể xem được các thông số training bằng graph.



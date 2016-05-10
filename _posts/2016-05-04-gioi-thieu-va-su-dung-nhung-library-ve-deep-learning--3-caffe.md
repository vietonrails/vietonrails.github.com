---
layout: post
title: "Giới thiệu và sử dụng những library về Deep Learning : 3. Caffe"
description: ""
category: AI
tags: [deep learning, caffe]
comments: true
---
Caffe là 1 library về deep learning được viết bằng C++. Caffe được phát triển nhờ vào phòng nghiên cứu BVLC thuộc đại học California, có thể sử dụng cho cả C++, Python hay MATLAB. 

Ngày open chính thức thì không rõ, nhưng đã xuất hiện trước chainer hay TF. Bạn có thể tìm thấy khá nhiều bài viết thú vị sử dụng caffe. 

<!-- more -->

## install

Để install được caffe khá vất vả. Cũng phải thông cảm vì caffe được phát triển từ 1 phòng nghiên cứu, thì không thể chuyên nghiệp như Google hay 1 công ty chuyên về AI được :).

Caffe cần khá nhiều library khác, khi install thì phải phụ thuộc vào cấu hình máy cụ thể, vì thế rất tốn thời gian và stress. 

Quá trình cài caffe sẽ khá vất vả và tốn thời gian vì những lỗi "định mệnh". Vì thế mong bạn hãy kiên nhẫn =)

### install các libs liên quan

```bash
$ brew install --fresh -vd snappy leveldb gflags glog szip lmdb
$ brew tap homebrew/science
$ brew install hdf5 opencv
$ brew install --build-from-source --with-python --fresh -vd protobuf
$ brew install --build-from-source --fresh -vd boost boost-python
$ brew install openblas
```

* `opencv` là tốn thời gian nhất :|. 
* OpenBLAS nên có để các bước sau tránh được các lỗi khi tạo makefile. 

### clone caffe

```bash
$ git clone https://github.com/BVLC/caffe.git
```

### Makefile.config

```bash
$ cd caffe
$ cp Makefile.config.example Makefile.config
```

Sau đó ta chỉnh sửa file này như sau : 

* `# CPU_ONLY := 1` bỏ comment out dòng này 
* Sửa `BLAS := atlas` thành `BLAS := open`
* Bỏ comment out 2 dòng dưới 

```bash
# BLAS_INCLUDE := $(shell brew --prefix openblas)/include
# BLAS_LIB := $(shell brew --prefix openblas)/lib
```

* Sửa lại `PYTHON_INCLUDE` cho đúng với máy. 

Trước : 

```bash
PYTHON_INCLUDE := /usr/include/python2.7 \ /usr/lib/python2.7/dist-packages/numpy/core/include
```

Sau : 

```bash
PYTHON_INCLUDE := /usr/local/Cellar/python/2.7.11/Frameworks/Python.framework/Versions/2.7/include/python2.7 \ /usr/local/lib/python2.7/site-packages/numpy/core/include/
```

### Build & test

```bash
$ make clean
$ make all -j4
$ make test -j4
$ make runtest
```
Nếu không có vấn đề gì thì kết quả sẽ như sau : 

```bash
[  PASSED  ] 927 tests.
```

### install PyCaffe

Đến folder `caffe/python` và cài các libs mà PyCaffe cần : 

```bash
$ cd python/
$ for li in $(cat requirements.txt); do sudo pip install $li; done 
```

### Build PyCaffe

```bash
$ cd ../
$ make pycaffe
$ make distribute
```

### config environment

Config các tham số cho Caffe. Thêm vào trong `~/.bashrc` dòng sau :

```bash
export PYTHONPATH={caffe/python path}:$PYTHONPATH
```

```bash
$ source ~/.bashrc
```

### Test

Sử dụng Python và import caffe vào, nếu không vấn đề gì thì quá trình install đã thành công. 

```bash
$ python 
>>> import caffe
```

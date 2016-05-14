---
layout: post
title: "Cơ bản về tensorflow"
description: ""
category: AI
tags: [tensorflow, deeplearning]
comments: true
author: rockkhuya
---
## DeepLearning là gì ? 

Bài viết này hướng dẫn và giải thích các method, cấu trúc của TensorFlow. Yêu cầu là bạn cần biết 1 chút về các khái niệm cơ bản về Machine learning, Deep learning. Những khái niệm này mình nghĩ các bạn nên đọc 1 tài liệu chuyên sâu nào thì tốt hơn :D.

Ví dụ về machine learning : 
Cho 1 tập hợp các điểm trong mặt phẳng. Tìm hàm số gần đúng nhất đi qua các điểm đó. 

Các điểm trong thực tế luôn có sai số, nên tìm 1 hàm đi qua chính xác tất cả các điểm là điều gần như không thể.
Machine learning là 1 thuật toán giúp tìm 1 hàm gần đúng sao cho sai số tổng thể là chấp nhận được. 

![Bài toán tìm hàm số gần đúng](https://qiita-image-store.s3.amazonaws.com/0/63543/70bcbddc-02e2-f091-8e21-8de0d2855e6c.gif)

Trong thực tế luôn có những bài toán như thế : cho dữ liệu về đồ thị của 1 cổ phiếu, tìm hàm số gần đúng nhất để dự đoán xem ngày mai giá cổ phiếu lên hay xuống :D. 

Ví dụ 2 : cho tập hợp các điểm trong mặt phẳng. Phân loại tập hợp thành các group sao cho sai số nhỏ nhất có thể. 

![Bài toán phân loại tập hợp](https://qiita-image-store.s3.amazonaws.com/0/63543/5a550314-d14d-a1c0-9d9a-3de7557a04e8.gif)

Đặc điểm của machine learning, deep learning là các thuật toán gần đúng và lặp đi lặp lại để kết quả sau gần đúng với kết quả nhất. 


## Các khái niệm cơ bản

### Tensor

Tensor là khái niệm cơ bản nhất trong TensorFlow. 

* Tensor là cấu trúc dữ liệu được sử dụng trong toàn TensorFlow, đại diện cho tất cả các loại dữ liệu. Hay nói cách khác là tất cả các loại dữ liệu đều là tensor. 
* Việc trao đổi dữ liệu trong quá trình xử lý chỉ thông qua tensor.
* Hiểu đơn giản thì tensor là mảng n chiều hay list cộng thêm 1 vài thứ thú vị khác. 
* Tensor có 3 thuộc tính là Rank, Shape, Type.

### Rank

`t = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]` có Rank 2
Rank là số chiều của dữ liệu.

| Rank | đơn vị số học | Python example |
|---|---|---|
| 0 | Scalar | s = 483 |
| 1 | Vector | v = [1.1, 2.2, 3.3] |
| 2 | Matrix | m = [[1, 2, 3], [4, 5, 6], [7, 8, 9]] |
| 3 | 3-Tensor | t = [[[2], [4], [6]], [[8], [10], [12]], [[14], [16], [18]]] |
| n | n-Tensor | (n chiều)  .... |

### Shape

`t = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]` có shape là   `[3, 3]`. 
Nói cách khác thì Shape là chiều của tensor. 
Ví dụ `t = [[[2], [4], [6]], [[8], [10], [12]], [[14], [16], [18]]]` có shape là `[1, 3, 3]`.

|  Rank | Shape | Dimension number | Example |
|-|-|-|-|
| 0 | [] | 0-D | A 0-D tensor. A scalar. |
| 1 | [D0] | 1-D | A 1-D tensor with shape [5]. |
| 2 | [D0, D1] | 2-D | A 2-D tensor with shape [3, 4]. |
| 3 | [D0, D1, D2] | 3-D | A 3-D tensor with shape [1, 4, 3]. |
| n | [D0, D1, ... Dn-1] | n-D | A tensor with shape [D0, D1, ... Dn-1].|

### Type

Các dạng số trong TF.

| Data type | Python type | Description |
|-|-|-|
| DT_FLOAT | tf.float32 | 32 bits floating point.|
| DT_DOUBLE | tf.float64 | 64 bits floating point.|
| DT_INT8 | tf.int8 | 8 bits signed integer.|
| DT_INT16 | tf.int16 | 16 bits signed integer. |
| DT_INT32 | tf.int32 | 32 bits signed integer. |
| DT_INT64 | tf.int64 | 64 bits signed integer. |
| DT_UINT8 | tf.uint8 | 8 bits unsigned integer. |
| DT_STRING | tf.string | Variable length byte arrays. Each element of a Tensor is a byte array. |
| DT_BOOL | tf.bool |  Boolean. |
| DT_COMPLEX64| tf.complex64| Complex number made of two 32 bits floating points: real and imaginary parts.|
|DT_QINT8|  tf.qint8| 8 bits signed integer used in quantized Ops. |
|DT_QINT32| tf.qint32|  32 bits signed integer used in quantized Ops.|
| DT_QUINT8|  tf.quint8|  8 bits unsigned integer used in quantized Ops.|

### Variable

Variable lưu trạng thái (state) sau khi tính toán graph. 

## MNIST

Trong dữ liệu MNIST có 55000 bức ảnh mẫu (images)Tensor cùng với label tương ứng (label)Tensor. 

`Images Tensor` có `Shape[55000, 784]`, `Rank 2`, `dtype = tf.float32`
`Labels Tensor` có `Shape[55000, 10]`, `Rank 2`, `dtype = tf.float32`

Tiếp theo là load dữ liệu vào bằng `tf.placeholder`. 

```python
x = tf.placeholder(tf.float32, [None, 784]) #images
y_ = tf.placeholder(tf.float32, [None, 10]) #labels
# None ở đây sẽ được thay bằng số batch
```

Mỗi lần thực hiện training trong `tf.placeholder()` thì đều cần param `feed_dict` để truyền dữ liệu.

Trong tutorial phần cuối có đoạn code sau : 

```python
for i in range(1000):
  batch_xs, batch_ys = mnist.train.next_batch(100)
  sess.run(train_step, feed_dict={x: batch_xs, y_: batch_ys})
```

Trong tutorial thì `x:Shape[100, 784] y_:Shape[100, 10]` nên mỗi lần training TF sẽ training 100 bức ảnh. 

### Xử lý ảnh

Bạn có thắc mắc con số 784 ở trên không ? Nếu có thì phần này sẽ giải thích lý do tại sao. 

Mỗi bức ảnh trong MNIST là 28x28px có kiểu màu sắc là gray scale. Gray scale là kiểu hiển thị 2 màu đen (1) và trắng (0), ở giữa là độ tối của màu. 

![gray scale](http://hp.vector.co.jp/authors/VA003180/kisha/gr_scale.gif)

Gray scale được tính là 1 channel, nên 1 bức ảnh nếu được flat ra thành 1 vector có 784 chiều. Ta có : 
` 28 * 28 * 1 = 784 - Dimension`

Dưới đây là ví dụ về số 1 : 

![number 1](https://qiita-image-store.s3.amazonaws.com/0/63543/0819b8b1-ff1b-f765-9edc-37c6ff636fa8.jpeg)

Flat ra thành vector ta có : 
```
00000000000000000000000000000000000000000000000000000000000000000000000000000000000000.6.7.7.50000000000.81111111.9.30000000.4.4.4.7111000000000000.1.10000000000000000000000000000000000000000000000000000000000
```

Hiển thị dưới dạng đồ thị : 

![Graph number 1](https://qiita-image-store.s3.amazonaws.com/0/63543/4b2aec34-2d8b-1c94-91f9-5240ff6048f0.jpeg)

Ở đây, nếu ta không flat bức ảnh ra thì tensor sẽ có Shape là `[55000, 28, 28, 1]`, rank 4. 

Trong trường hợp ảnh màu, ta sẽ có 3 channels để hiển thị màu (RGB), lúc đó Shape `[55000, 28, 28, 3]`, rank 4. 

## Beginner tutorial

Để hiểu cách làm việc của TensorFlow, ta cần hiểu thêm 1 vài khái niệm khác nữa. Ở đây là **phép toán ma trận**, **trọng số**, **bias**, **hồi quy softmax**. 

### Phép toán ma trận

Ở đây ta có `x:[batch_num, 784]` nhân với ma trận `[784, 10]` sẽ cho kết quả là `[batch_num, 10]`, đây chính là ma trận kết quả labels. 

Về lý thuyết phép toán ma trận, bạn có thể tham khảo nhiều nguồn khác. 

Trong TF, phép toán ma trận được thực hiện như sau : 

```python 
# tf.matmul(A,B)
matmul = tf.matmul(x,W)
```
Ở đây : 

* x: [batch_num, 784]
* W: [784, 10]
* matmul: [batch_num, 10]

Ở đây, ma trận `[784, 10]` có vai trò rất quan trọng, được gọi là ** trọng số**.

### Trọng số

Trong tutorial, trọng số `W:[784, 10]` được định nghĩa bằng đoạn code sau : 

```python
W = tf.Variable(tf.zeros([784, 10]))
```

`tf.Variable()` được gọi là `in-memory buffers`, là biến số trong Tensor được sử dụng để lưu những parameters trong quá trình training. 
`tf.zeros()` là method tạo ra Tensor với khởi tạo tất cả là 0.

Khi muốn tạo ra giá trị khởi tạo là ngẫu số thì dùng method `tf.random_normal()`. 

#### Vai trò của trọng số

`W[784, 10]` : 784 tương ứng với 784 px của 1 bức ảnh, 10 tương ứng với 10 kết quả. `W[x, y]` thể hiện vai trò của pixel `x` trong tổng thể để kết quả nhận được là `y`.

Với ví dụ như ở trên, hàng trên cùng bên trái của `W` hầu hết là `0`. `W[0]` có kết quả là `[ 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.] `. 
Lý do vì các số từ 1 đến 9 hấu như không viết đến những pixel ở trên, do đó các pixel này không có giá trị gì cho việc đánh giá. 

Ngược lại, trọng số `W[380]` ở giữa sẽ có kết quả như sau : 
```
[-0.23017341 0.03032022 0.02670325 -0.06415708 0.07344861 -0.05119878 0.03592584 -0.00460929 0.09520938 0.08853132]
```

Ta thấy `W[380, 0]` có giá trị là nhỏ nhất `-0.23017341`, điều này đồng nghĩa với việc : nếu pixel `380` này có giá trị là 1 thì xác xuất kết quả là số 0 là nhỏ nhất. 

### Bias

Trong `y = a*x + b` thì `b` chínhl à bias.

### Hồi quy Softmax

Đến đây ta có : 
* Images Tensor `x:[batch_num, 784]`
* nhân với ma trận trọng số `W[784, 10]`
* được `matmul:[batch_num, 10]`
* cộng với bias `b:[10]`

Đến đây ta có thêm 1 bước là dùng method `tf.nn.softmax()` để tính toán những giá trị đó : 

```python
y = tf.nn.softmax(tf.matmul(x, W) + b)
print "y", y[0] # ảnh đầu tiên, kết quả là 7
y [ 2.04339485e-05 6.08732953e-10 5.19737077e-05   2.63350527e-03 2.94665284e-07 2.85405549e-05 2.29651920e-09 9.96997833e-01 1.14465665e-05 2.55984633e-04]     
```

Theo kết quả trên thì giá trị ở vị trí số 7 (tính từ 0) có giá trị lớn nhất. Suy ra xác suất là số 7 là cao nhất. 

Đến đây ta sẽ kiểm chứng lại kết quả : 

```python
x_answer = tf.argmax(y,1)
y_answer = tf.argmax(y_,1)

print "x",x_answer[0:10] # kết quả 10 bức ảnh đầu từ TF
print "y",y_answer[0:10] # kết quả thực tế 10 bức ảnh đầu 

x [7 2 1 0 4 1 4 9 6 9] 
y [7 2 1 0 4 1 4 9 5 9] 
```

Độ chính xác của kết quả tổng thế : 

```python
correct_prediction = tf.equal(tf.argmax(y,1), tf.argmax(y_,1))
accuracy = tf.reduce_mean(tf.cast(correct_prediction, "float"))

print "accuracy:", accuracy
accuracy: 0.9128    
```

## Training

Đến đây ta đã hiểu TF xử lý như thế nào.
Vấn đề còn lại là TF đã thực hiện việc tính toán `W` và `b` khi nào và như thế nào. 
Quá trình này gọi là quá trình training. Nói 1 cách đơn giản : dựa vào dữ liệu và kết quả thực tế cho trước, tìm ra 1 hàm số gần nhất thoả mãn bộ dữ liệu này.

Trong tutorial có 1 đoạn code thực hiện việc training này : 

```python
for i in range(1000):
  batch_xs, batch_ys = mnist.train.next_batch(100)
  sess.run(train_step, feed_dict={x: batch_xs, y_: batch_ys})
```

`train_step` đã thực hiện việc training này. Code trong method này như sau : 

```python
cross_entropy = -tf.reduce_sum(y_*tf.log(y))
train_step = tf.train.GradientDescentOptimizer(0.01).minimize(cross_entropy)
'''
 y: [batch_num, 10] y is a list of processed numbers of x(images)
y_: [batch_num, 10] y_ is labels
0.01 is a learning rate
'''
```

`tf.log()` dùng để tính `log`. Điều này không ảnh hưởng tới kết quả của Tensor nên `log-y:[batch_num, 10]`. 

Kết quả Tensor được nhân với `y_`, trong `y_` thì ngoài kết quả ra thì phần lớn là 0. 

Tensor - kết quả của phép nhân - có Shape là `[batch_num, 10]`, nhưng ngoài cầu trả lời ra thì phần lớn là 0, nên có thể hiểu là `[batch_num, 1]` cũng được. 

```python
log-y = tf.log(y)
print log-y[0]
[ -1.06416254e+01  -2.04846172e+01  -8.92418385e+00  -5.71210337e+00
  -1.47629070e+01  -1.18935766e+01  -1.92577553e+01  -3.63449310e-03      
  -1.08472376e+01  -8.88469982e+00]

y_times_log-y = y_*tf.log(y)
print y_times_log-y[0] # chỉ còn lại số 7
[-0.         -0.         -0.         -0.         -0.         -0. 
-0.        -0.00181153        -0.         -0.        ] 
```

`tf.reduce_sum()` thực hiện phép cộng ở tất cả các chiều, param thứ 2 là `keep_dims=True` nếu không có option này thì sẽ trả lại là Tensor có rank 0. 

```python
# 'x' is [[1, 1, 1]
#         [1, 1, 1]]
tf.reduce_sum(x) ==> 6
tf.reduce_sum(x, 0) ==> [2, 2, 2]
tf.reduce_sum(x, 1) ==> [3, 3]
tf.reduce_sum(x, 1, keep_dims=True) ==> [[3], [3]]
tf.reduce_sum(x, [0, 1]) ==> 6

------
cross_entropy = -tf.reduce_sum(y_*tf.log(y))

print "cross_entropy:", cross_entropy # giá trị tính tổng của tất cả các phần tử trong y_*tf.log(y)
cross_entropy 23026.0 # Giá trị trước khi training 
.
.
cross_entropy: 3089.6 # Giá trị sau khi training
```

`cross_entropy` nói 1 cách đơn giản là giá trị hiển thị mức độ training đạt được. 

## Trước khi training

```
cross_entropy 23026.0       
grad W[0] [ 0.  0.  0.  0.  0.  0.  0.  0.  0.  0.]                               
grad W[380] [ 511.78765869   59.3368187   -34.74549103 -163.8828125  -103.32589722
  181.61528015   17.56824303  -60.38471603 -175.52197266 -232.44744873]           
grad b [  19.99900627 -135.00904846  -32.00152588   -9.99949074   18.00206184     
  107.99274445   41.992836    -27.99754715   26.00336075   -8.99738121]           
```

## Sau khi training

```
cross_entropy 2870.42 
grad W[0] [ 0.  0.  0.  0.  0.  0.  0.  0.  0.  0.]                          
grad W[380] [  6.80800724   1.27235568  -6.85943699 -22.70822525 -17.48428154
  13.11752224  19.7425499  -32.00106812 -41.48160553  79.59416199]           
grad b [  19.52701187    3.17797041  -20.07606125  -48.88145447  -28.05920601
   37.52313232   40.22808456  -34.04494858  -74.16973114  104.77211761]      
```

Trọng số `W` ở pixel đầu tiên là `0` hết, có nghĩa là pixel này không có giá trị gì cả. 
Trọng số ở pixel `380` thì phức tạp hơn. Việc tính toán còn lại cứ để cho máy tính nhỉ :D. 



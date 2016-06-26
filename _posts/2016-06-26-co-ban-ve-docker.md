---
layout: post
title: "Cơ bản về Docker"
description: ""
category: server
tags: [docker]
comments: true
author: rockkhuya
---

## Docker là gì

Docker là 1 tools hỗ trợ việc tạo môi trường ảo (container) trên linux đơn giản và nhanh gọn hơn. Cũng vì được chú ý và phát triển rất nhanh nên nhiều thông tin cách đây 1 năm đã trở nên lỗi thời :D. 

## Đặc điểm của Docker

### Nhanh, nhẹ

Docker tạo OS ảo nên thời gian khởi động là rất nhanh. Điều này khác với vagrant, vagrant tạo môi trường ảo ở level phần cứng, nên khi khởi động mất nhiều thời gian hơn. Ngoài ra các container cũng sử dụng chung các images nên cũng không tốn nhiều disks. 

### Docker images, Docker Hub

Các files và settings được sử dụng trong container được  lưu và sử dụng lại, gọi chung là images của docker. Docker hub là nơi chia sẻ và lưu giữ các file images này (hiện có khoảng 300.000 images)

### Settings rõ ràng, hoàn toàn tự động

Bằng việc sử dụng các file settings như Dockerfile, các settings đều được định nghĩa rõ ràng bằng codes. Điều này giúp tự động hoá các thao tác và giảm thiểu lỗi thao tác trong quá trình sử dụng. 

### Hướng application

Mục đích chính của docker là dễ dàng thực hiện các thao tác build, run, deploy cho application. 

### Mềm dẻo, tự do

Docker gồm nhiều tools hỗ trợ, các tools này đều có thể chạy riêng rẽ, nên rất dễ sử dụng. Gần đây việc phân hoá components này diễn ra càng nhanh. 

## Sự khác biệt giữa container và máy ảo

Container thực hiện các process trên môi trường OS ảo. Điều này giúp container nhanh hơn, nhẹ hơn, đỡ tốn tài nguyên hơn so với máy ảo thông thường. Tuy nhiên, container chỉ có duy nhất OS là Linux. 

Gần đây cả Google và Amazon (AWS) đều có các service sử dụng container dạng này. 
* Google GKE, Google GCR
* Amazon ECS, Amazon ECR 

## Khái niệm cơ bản của docker

Docker có thể chia thành 5 phần chính như sau : 

### Container
Môi trường ảo tạo ra từ docker images.

### Docker images
Nơi chứa các files settings và hệ thống của containers.

### Docker server
Có thể nói đây là phần chính của docker, nơi quản lý các containers, images. 

### Docker client
Là gọi chung các tools GUI, command để users thao tác trên docker. 

### Docker hub (repo)
Là sites lưu trữ và chia sẻ các images Docker.

## Các command cơ bản của docker

### Run (docker run)
khởi động và chạy 1 image docker.

Ví dụ : hello world

```
$ docker run hello-world
```

Ví dụ : Ubuntu

```
$ docker run -i -t ubuntu /bin/bash
```

Ví dụ : Nginx với cổng 8080

```
$ docker run -p 8080:80 nginx
```

### List các containers (docker ps)
Hiển thị danh sách các containers. Nếu muốn hiển thị các các container đã dừng hoạt động thì dùng flag `-a`.

```
$ docker ps
```

### Tắt container (docker kill)
Tắt 1 container ( không phải là xoá nhé :D )

```
$ docker kill [containerID/name]
```

### Xoá container (docker rm)
Xoá 1 container. 

```
$ docker rm [containerID/name]
```

Ngoài các command trên ra, docker còn rất nhiều command khác nữa. Các bạn có thể xem trong trang web chính thức của docker. 

## Dockerfile

File thiết lập cấu trúc cho image docker. File này được viết dưới dạng code. 

Ví dụ về Dockerfile (Nginx)

```
# Nginx
#
# VERSION               0.0.1

FROM      ubuntu
MAINTAINER Victor Vieux <victor@docker.com>

LABEL Description="This image is used to start the foobar executable" Vendor="ACME Products" Version="1.0"
RUN apt-get update && apt-get install -y inotify-tools nginx apache2 openssh-server
```

Command để build docker images : 

```
$ docker build -t mynginx .
```

Trong bài viết tiếp theo ta sẽ thử sử dụng docker vào thực tế bằng cách build môi trường dev cho Rails 5. 

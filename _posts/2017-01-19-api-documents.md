---
layout: post
title: "Vài điều về API documents"
description: "Phân loại API documents và các tools hỗ trợ."
category: API
tags: [API, documents]
comments: true
author: thuckhuya
---

Viết document cho API luôn là phần bị “phân biệt đối xử” theo kiểu : “để cuối cùng làm” hay “làm cho có”. 

Vấn đề này xuất phát từ 1 vài lý do sau : 

1. Tâm lý thích code hơn thích viết document. 
2. Phần document thường không được estimate trong hợp đồng, nên dành thời gian để viết document là “tốn công vô ích”. 
3. Giữa thiết kế và thực hiện luôn có sự khác biệt. Mỗi 1 thay đổi như thế cần phải cập nhật lại document, nên gây tâm lý “để cuối cùng làm”.

Tuy nhiên cần phải khẳng định rằng document là bộ mặt của 1 project. Thử tưởng tượng có ai thả bạn giữa Hà Nội mà không có bản đồ xem :D. 

Về cách tiếp cận document, mình chia thành 2 loại : 

1. Document có trước, code sau. 
2. Vừa code vừa viết document. 

<!-- more -->

## Document có trước, code sau

Đây thường là các dự án lớn, có thời gian để chuẩn bị và nghiên cứu requirement khá kĩ. Ngoài ra trong team cũng cần có 1 người dày dặn kinh nghiệm để lường trước tất cả các tình huống sau này. 

Ưu điểm của cách này hệ thống có thiết kế rõ ràng, thuận lợi cho việc triển khai sau này. 
Nhược điểm là mỗi lần thay đổi thì phải confirm với khách hàng. Việc này thường tốn khá nhiều thời gian. 

Về document dạng này, mình thấy có 2 tools rất hay sau : 

1. [Swagger – The World’s Most Popular Framework for APIs.](http://swagger.io/)
2. [Apiary - Platform for API Design, Development & Documentation](https://apiary.io/) 

Swagger thì miễn phí nhưng không có chức năng tạo mock server (thực ra là có nhưng vô cùng tệ, bản mock server cho Rails thậm chí còn bị lỗi ... sai tên file `controller` với `controllers`). APIARY thì tuyệt vời, có thể tạo mock server bằng vài cú click. Bản free của APIARY cũng vừa đủ cho 1 team nhỏ nhỏ. 

## Vừa code vừa viết document

Cách này phù hợp với các dự án nhỏ, yêu cầu gấp, document không quá phức tạp. Ưu điểm của cách này là có thể bắt tay vào làm ngay. Tuy nhiên nhược điểm là dễ bị “để cuối làm”. 

Để khắc phục tâm lý này, mình thường yêu cầu tất cả các Pull Request phải update document thì mới được merge.

Đối với rails, mình thường dùng gem [apipie-rails](https://github.com/Apipie/apipie-rails).

Ở bài viết sau mình sẽ giải thích kĩ hơn cách mình config và sử dụng apipie trong 1 dự án Rails. 

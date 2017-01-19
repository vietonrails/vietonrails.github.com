---
layout: post
title: "Sử dụng apipie để viết document cho rails"
description: "Sử dụng apipie để viết document cho Rails"
category: API
tags: [API, documents, apipie]
comments: true
author: thuckhuya
---
[apipie] [GitHub - Apipie/apipie-rails: Ruby on Rails API documentation tool](https://github.com/Apipie/apipie-rails)

`apipie` là gem hỗ trợ viết document cho RESTful API. Các ưu điểm của gem này là : 

1. Viết document mà như code bằng Ruby =)). Đỡ tốn công học thêm các syntax khác. 
2. Reusing rất dễ dàng.
3. Việc quản lý document cũng khá đơn giản. Có thể gộp chung cùng source code để quản lý bằng git. 

Việc dùng gem này như thế nào bạn có thể tham khảo github của gem. Mình ở đây chỉ nói về các tricks khi dùng `apipie`. 

## Cấu trúc folders

Trong hướng dẫn trên github, document được viết luôn trong `controller`. Nhưng mình thấy như thế rất tệ. Lý do vì : 

1.  `controller` phình to ra, khó quản lý, rối mắt. 
2. Khi review thì document và code không tách bạch nhau ra. Gây khó khăn cho review. 

Cách giải quyết của mình là sử dụng `include`. Ví dụ : 

```ruby
# app/controllers/api/v1/lists_controller.rb
module Api::V1
  class ListsController < ApiController
    include ListsDoc
    ...
    end
end
```

Mình thường đặt toàn bộ document trong thư mục `app/docs`. 
Như vậy `ListsDoc` sẽ được định nghĩa trong file `app/docs/lists_doc.rb`. 

```ruby
# app/docs/lists_doc.rb
module ListsDoc
  extend Apipie::DSL::Concern

  api :GET, '/lists/:id', '(Done) Get lists'
  param :id, :number, required: true
  param :page, :number
  param :limit, :number
  error :code => 401, :desc => "Unauthorized"
  error :code => 404, :desc => "Not Found"
  def show
  end
    …
end
```

Như vậy `controller` chỉ thêm vào 1 dòng duy nhất. Tất cả các thay đổi document đều được thực hiện trong thư mục `app/docs`. 

> Tất cả các file trong thư mục `app` được được Rails autoload. 

## Show db schema
Việc thay đổi DB trong quá trình làm dự án là điều khó tránh khỏi. Mỗi lần như thế lại phải update lại ER diagram, mà các file diagram này thường được share qua Google Driver hay Dropbox, giờ muốn đọc document và xem ER thì cũng phiền phức :D. 

Trong rails có file `db/schema.rb` để define các table. File này được cập nhật mỗi khi chạy migration, vì thế nó luôn đảm bảo là DB mới nhất. 

Giờ ta sẽ đọc file này và show lên document : 

```
module DbDoc
  extend Apipie::DSL::Concern
  api :GET, '/db/', 'DB schema.'
  description "DB schema."
  example <<-EXAMPLE
  #{File.read('db/schema.rb') if File.exist?('db/schema.rb')}
  EXAMPLE
  def db_schema
  end
end
```

## Tạo resource là common
Mình thường tạo thêm 1 `resource` là `common` để cho các document khác vào đây. Ví dụ như : 

- Authentication
- DB schema
- …

## Phải sửa document và cho vào PR
1 quy ước nhỏ để đảm bảo document luôn được làm cẩn thận và cập nhật. 
Mình có thêm 1 label `need document`, sau khi review và code đã sẵn sàng merge, mình sẽ dùng label này để các bạn update document mới nhất lên.

## 1 số vấn đề của apipie
Sau 1 thời gian dùng apipie, mình thấy có 1 số vấn đề sau : 

1. Không order được các `resources`. Các `resources` luôn được sắp xếp theo thứ tự ABC. 
2. Không order được các element. Phần `params`  lẽ ra phải đặt trước phần `example`, nhưng vì bắt đầu là `p` nên luôn bị xếp dưới cùng. 
3. Không có chức năng mock server. 
---
layout: post
title: "[Rails] Vì sao lại nên dùng scope hơn class method ?"
description: ""
category: Rails
tags: [active record, scopes, class methods]
comments: true
author: rockkhuya
---

[Active Record scopes vs class methods](http://blog.plataformatec.com.br/2013/02/active-record-scopes-vs-class-methods/)
Đây là 1 bài viết rất hay về sự khác biệt giữa scope và class method, nếu có thể bạn nên đọc bản gốc, bài viết này mình sẽ chỉ tóm tắt những ý chính theo dạng memo cá nhân thôi.

<!-- more -->

## Về bản chất thì scope là class method
Trong Rails thì scope được định nghĩa như là 1 class method động.

```ruby
def self.scope(name, body)
  singleton_class.send(:define_method, name, &body)
end
```

Ví dụ như với scope dưới đây :

```ruby
  scope :published, -> {where(status: 'published')}
```

Scope này sẽ được triển khai thành :

```ruby
def self.published
  where(status: 'published')
end
```

Về mặt bản chất, scope là 1 class method. Vậy tại sao lại nên dùng scope hơn class method ? Dưới đây là 2 lý do :

## scope luôn đảm bảo sẽ thực hiện method chain
Giả sử ta có 2 scope như sau :

```ruby
class Post < ActiveRecord::Base
  scope :by_status, -> status { where(status: status) }
  scope :recent, -> { order("posts.updated_at DESC") }
end
```

Như trong model này, có vẻ như viết dưới dạng class method cũng vẫn đảm bảo khả năng method chain.  Tuy nhiên, trong trường hợp tham số `status` là `nil` hoặc `blank` thì sẽ thế nào ?

```ruby
Post.by_status(nil).recent
# SELECT "posts".* FROM "posts" WHERE "posts"."status" IS NULL
#   ORDER BY posts.updated_at DESC

Post.by_status('').recent
# SELECT "posts".* FROM "posts" WHERE "posts"."status" = ''
#   ORDER BY posts.updated_at DESC
```

Thông thường, nếu ta không muốn phát sinh ra những query như thế thì ta sẽ sửa thành :

```ruby
scope :by_status, -> status { where(status: status) if status.present? }
```

Với cách viết như trên, các scope chắc chắn sẽ thực hiện chain mà không gặp phải vấn đề gì.

Vậy với class method thì sao ? Code lúc ấy sẽ như sau :

```ruby
class Post < ActiveRecord::Base
  def self.by_status(status)
    where(status: status) if status.present?
  end
end

Post.by_status('').recent
NoMethodError: undefined method `recent' for nil:NilClass
```

Trong trường hợp những query phát sinh trong scope là `nil`, scope sẽ trả về `.all`, vì thế mà quá trình chain vẫn diễn ra bình thường.

## scope có thể mở rộng được
Trong các thư viện pagination, cách viết như sau thường được sử dụng :

```ruby
Post.page(2).per(15)

posts = Post.page(2)
posts.total_pages # => 2
posts.first_page? # => false
posts.last_page?  # => true
```

Các method như `.per`, `total_pages`, `first_page?`, `last_pages?`, ... ta chỉ muốn gọi lên sau khi đã gọi `.page`. Khi đó ta sẽ viết dưới dạng `scope extensions`.

```ruby
scope :page, -> num { # some limit + offset logic here for pagination } do
  def per(num)
    # more logic here
  end

  def total_pages
    # some more here
  end

  def first_page?
    # and a bit more
  end

  def last_page?
    # and so on
  end
end
```

Với cùng mục đích như vậy, nếu viết dưới dạng class method sẽ như sau :

```ruby
def self.page(num)
  scope = # some limit + offset logic here for pagination
  scope.extend PaginationExtensions
  scope
end

module PaginationExtensions
  def per(num)
    # more logic here
  end

  def total_pages
    # some more here
  end

  def first_page?
    # and a bit more
  end

  def last_page?
    # and so on
  end
end
```

#Kết luận
Cách viết scope có nhiều ưu điểm hơn cách viết class method, tuy nhiên, không nên sử dụng scope một cách cực đoan. Quan trọng nhất là sử dụng với đúng mục đích và yêu cầu của công việc.


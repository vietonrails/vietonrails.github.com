---
layout: post
title: "Code theo phong cách Ruby (1)"
description: ""
category: Ruby
tags: [code style, tips]
comments: true
---
Bài viết này liệt kê những cách viết code theo phong cách Ruby, ngắn gọn và dễ nhìn hơn.


## Đặt if ở phía sau để rút gọn

```ruby
if user.active?
  send_mail_to(user)
end
```

```ruby
send_mail_to(user) if user.active?
```

## Dùng unless thay cho if + not

```ruby
user.destroy if !user.active?
```

```ruby
user.destroy unless user.active?
```

Tuy nhiên, nếu điều kiện phía sau unless phức tạp, có `and`, `or`, thì nên dùng `if` cho dễ hiểu.

```ruby
user.destroy unless (user.active? || user.admin?) && !user.spam?
```

<!-- more -->

## Sử dụng cách viết điều kiện thu gọn

```ruby
if user.admin?
  "I appreciate for that."
else
  "Thanks."
end
```

```ruby
user.admin? ? "I appreciate for that." : "Thanks"
```

Tuy nhiên, chỉ nên dùng cho những đoạn code có điều kiện đơn giản, không nên dùng với những đoạn code lồng nhau.

```ruby
# khó đọc
user.admin? ? user.active? ? "I appreciate for that." : "Are you OK?" : "Thanks."
```

## Dùng if đồng thời với phép gán

```ruby
user = find_user
if user
  send_mail_to(user)
end
```

```ruby
if user = find_user
  send_mail_to(user)
end
```

Tuy nhiên, cách viết này có thể gây hiểu nhầm cho người đọc: "lỗi type gõ thiếu dấu `=`, đáng ra phải là `==` hoặc `!=` chứ". Vì thế có người thích mà cũng có người không thích cách viết này.

## Xác nhận điều kiện từ các class con

```ruby
if parent.children
  if parent.children.singleton?
    singleton = parent.children.first
    send_mail_to(singleton)
  end
end
```

Viết gọn lại
```ruby
if parent.children && parent.children.singleton?
  singleton = parent.children.first
  send_mail_to(singleton)
end
```


## Không dùng return ở cuối method

Các ngôn ngữ khác phải cần, nhưng với ruby, cách viết không có return có vẻ được yêu thích hơn.

```ruby
def build_message(user)
  message = 'hello'
  message += '!!' if user.admin?
  return message
end
```

```ruby
def build_message(user)
  message = 'hello'
  message += '!!' if user.admin?
  message
end
```

## Sử dụng Object#tap

```ruby
def build_user
  user = User.new
  user.email = "hoge@hoge.com"
  user.name = "Taro Yamada"
  user
end
```

```ruby
def build_user
  User.new.tap do |user|
    user.email = "hoge@hoge.com"
    user.name = "Taro Yamada"
  end
end
```


## Khi ghép các chuỗi kí tự, không dùng "+” mà dùng "#{ }"

```ruby
"Hello, " + user.name + "!"
```

```ruby
"Hello, #{user.name}!"
```

### Freeze các hằng số

`freeze` là cách khai báo một hằng số. Dùng cách này để tránh trường hợp hằng số bị thay đổi trong quá trình làm việc.

### Chuỗi kí tự

```ruby
CONTACT_PHONE_NUMBER = "03-1234-5678"
CONTACT_PHONE_NUMBER << "@#$%^"
puts CONTACT_PHONE_NUMBER # => 03-1234-5678@#$%^
```

```ruby
CONTACT_PHONE_NUMBER = "03-1234-5678".freeze
CONTACT_PHONE_NUMBER << "@#$%^" # => RuntimeError: can't modify frozen String
```

### Array

```ruby
ADMIN_NAMES = ["Tom", "Alice"]
ADMIN_NAMES << "Taro"
puts ADMIN_NAMES # => ["Tom", "Alice", "Taro"]
```

```ruby
ADMIN_NAMES = ["Tom", "Alice"].freeze
ADMIN_NAMES << "Taro" # => RuntimeError: can't modify frozen Array
```

## Khi tạo 1 array,  dùng %w( )、%i( ) thay cho []

Trường hợp muốn tạo 1 array các chuỗi kí tự, dùng `%w( )` sẽ dễ viết và ngắn hơn.

```ruby
actions = ['index', 'new', 'create']
```

```ruby
actions = %w(index new create) # => ['index', 'new', 'create']
```

Từ Ruby 2.0 trở đi, có cách viết `%i( )` để tạo symbol.

```ruby
actions = %i(index new create) # => [:index, :new, :create]
```

## Khi xử lý 1 array theo thứ tự,  dùng "&:method" thay cho "object.method"

```ruby
names = users.map{|user| user.name }
```

```ruby
names = users.map(&:name)
```

Không chỉ có `map` mà cả `each` ,`select` hay các block khác đều có thể dùng cách viết này.


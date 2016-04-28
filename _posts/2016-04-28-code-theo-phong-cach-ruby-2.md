---
layout: post
title: "code theo phong cách Ruby (2)"
description: ""
category: Ruby
tags: [code style, tips]
comments: true
---

# Khi khai báo 1 số lớn, thêm "_" vào sẽ dễ đọc hơn

```ruby
ITEM_LIMIT = 1000000000
```

```ruby
ITEM_LIMIT = 1_000_000_000
```

# dùng attr_reader thay cho những method getter đơn giản

```ruby
class Person
  def initialize
    @name = "No name"
  end

  def name
    @name
  end
end
```

```ruby
class Person
  attr_reader :name

  def initialize
    @name = "No name"
  end

  # Không cần
  # def name
  #   @name
  # end
end
```
<!-- more -->


# Trong 1 array mà mỗi phần tử mang 1 ý nghĩa khác nhau, có thể lấy vào nhiều biến cùng lúc

```ruby
ans_array = 14.divmod(3)
puts "Thương #{ans_array[0]}"     # => Thương 4
puts "Số dư  #{ans_array[1]}"     # => Số dư  2
```

```ruby
quotient, remainder = 14.divmod(3)
puts "Thương #{quotient}"      # => Thương 4
puts "Số dư  #{remainder}"     # => Số dư  2
```

Tương tự với method `each` của 1 hash. 

```ruby
# key và value được lấy về dưới dạng array
{name: 'Tom', email: 'hoge@hoge.com'}.each do |key_and_value|
  puts "key: #{key_and_value[0]}"
  puts "value: #{key_and_value[1]}"
end
```

```ruby
# key và value được lưu vào 2 biến khác nhau
{name: 'Tom', email: 'hoge@hoge.com'}.each do |key, value|
  puts "key: #{key}"
  puts "value: #{value}"
end
```

# Khi nối nhiều array với nhau, không dùng + mà nên dùng *(splat)

```ruby
numbers = [1, 2, 3]
numbers_with_zero_and_100 = [0] + numbers + [100] # => [0, 1, 2, 3, 100]
```

```ruby
numbers = [1, 2, 3]
numbers_with_zero_and_100 = [0, *numbers, 100] # => [0, 1, 2, 3, 100]
```

Trong trường hợp bạn quên mất dấu `*` thì kết quả sẽ như sau :

```ruby
[0, numbers, 100] # => [0, [1, 2, 3], 100]
```

# Cách viết ||= khi cần "nếu nil thì init"

```ruby
def twitter_client
  @twitter_client = Twitter::REST::Client.new if @twitter_client.nil?
  @twitter_client
end
```

```ruby
def twitter_client
  @twitter_client ||= Twitter::REST::Client.new 
end
```

# Khi toàn bộ method là đối tượng của rescue thì lược bỏ begin/end

```ruby
def process_user(user)
  begin
    send_to_mail(user)
  rescue
    # xử lý 
  end
end
```

```ruby
def process_user(user)
  send_to_mail(user)
rescue
  # xử lý
end
```

# gọi rescue là StandardError thay vì Exception
Với những người đã từng học qua Java hay C# thì thường có thói quen sử dụng `Exception` để bắt lỗi ngoại lệ. 
Tuy nhiên, khi bắt `Exception` trong Ruby cũng đồng nghĩa với việc bắt luôn những lỗi cực kì nguy hiểm như `NoMemoryError`. 

`StandardError` là 1 subclass của `Exception` để bắt lỗi thực hiện của code. rescue mặc định là sẽ bắt `StandardError` và các subclass của nó nên có thể bỏ qua khi viết code. 

```ruby
def process_user(user)
  send_to_mail(user)
rescue Exception => ex
  # bắt cả những lỗi nguy hiểm như NoMemoryError,  ...
end
```

```ruby
def process_user(user)
  send_to_mail(user)
rescue => ex
  # bắt tất cả các lỗi thực hiện (StandardError và các subclass của nó)
end
```

# Khi thao tác với index, nêu dùng -1 thay vì size - 1
Code khi sử dụng size - 1
```ruby
numbers = [1, 2, 3, 4, 5]
name = 'Viet on Rails'

numbers[numbers.size - 1] # => 5
name[name.size - 1] # => 's'

numbers[1..numbers.size - 2] # => [2, 3, 4]
name[1..name.size - 2] # => "iet on Rail"
```

Code khi sử dụng -1
```ruby
numbers = [1, 2, 3, 4, 5]
name = 'Viet on Rails'

numbers[-1] # => 5
name[-1] # => 's'

numbers[1..-2] # => [2, 3, 4]
name[1..-2] # => "iet on Rail"
```

# find: trả lại yếu tố đầu tiên tìm thấy

```ruby
def find_admin(users)
  users.each do |user|
    return user if user.admin?
  end
  nil
end
```

```ruby
def find_admin(users)
  users.find(&:admin?)
end
```

Sử dụng `find_index` nếu muốn trả về index của yếu tố đầu tiên tìm thấy. 

# select: lấy tất cả những yếu tố thoả mãn điều kiện

```ruby
def find_admins(users)
  admins = []
  users.each do |user|
    admins << user if user.admin?
  end
  admins
end
```

```ruby
def find_admins(users)
  users.select(&:admin?)
end
```

Trái với `select` là `reject`, hàm này chỉ lấy những yếu tố `false`.


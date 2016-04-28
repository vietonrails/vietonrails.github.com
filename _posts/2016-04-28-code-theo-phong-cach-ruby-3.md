---
layout: post
title: "code theo phong cách Ruby (3)"
description: ""
category: Ruby
tags: [code style, tips]
comments: true
---

# count: đếm số yếu tố thoả mãn điều kiện

```ruby
def count_admin(users)
  count = 0
  users.each do |user|
    count += 1 if user.admin?
  end
  count
end
```

```ruby
def count_admin(users)
  users.count(&:admin?)
end
```

# map: tạo ra 1 array từ 1 array khác

```ruby
def user_names(users)
  names = []
  users.each do |user|
    names << user.name
  end
  names
end
```

```ruby
def user_names(users)
  users.map(&:name)
end
```

<!-- more -->

# flat_map: kết quả của map sẽ được làm "phẳng"

```ruby
nested_array = [[1, 2, 3], [4, 5, 6]]
mapped_array = nested_array.map {|array| array.map {|n| n * 10 } }
# => [[10, 20, 30], [40, 50, 60]]
flat_array = mapped_array.flatten
# => [10, 20, 30, 40, 50, 60]
```

```ruby
nested_array = [[1, 2, 3], [4, 5, 6]]
flat_array = nested_array.flat_map {|array| array.map {|n| n * 10 } }
# => [10, 20, 30, 40, 50, 60]
```



# compact: lấy các yếu tố khác nil

```ruby
numbmers_and_nil = [1, 2, 3, nil, nil, 6]
only_numbers = numbmers_and_nil.reject(&:nil?) # => [1, 2, 3, 6]
```

```ruby
numbers_and_nil = [1, 2, 3, nil, nil, 6]
only_numbers = numbers_and_nil.compact # => [1, 2, 3, 6]
```

# any?: kiểm tra có bất kì 1 yếu tố nào thoả mãn hay không

```ruby
def contains_nil?(users)
  users.each do |user|
    return true if user.nil?
  end
  false
end
```

```ruby
def contains_nil?(users)
  users.any?(&:nil?)
end
```

`all?` trả về `true` nếu tất cả các yếu tố đều thoả mãn điều kiện. 

# empty?: nếu không có yếu tố nào thì sẽ trả về true

```ruby
puts "empty!" if users.size == 0
```

```ruby
puts "empty!" if users.empty?
```

# first/last: trả lại yếu tố đầu tiên / cuối cùng 

```ruby
first_user = users[0]
last_user = users[users.size - 1]
```

```ruby
first_user = users.first
last_user = users.last
```

# sample: lấy ngẫu nhiên 1 yếu tố

```ruby
users[rand(users.size)]
```

```ruby
users.sample
```

# each_with_index: vòng lặp each với index

```ruby
counter = 0
users.each do |user|
  puts ", " if counter > 0
  puts user.name
  counter += 1
end
```

```ruby
users.each_with_index |user, counter|
  puts ", " if counter > 0
  puts user.name
end
```

# join: chuyển 1 array về thành 1 chuỗi

```ruby
def numbers_text(numbers)
  text = ''
  numbers.each_with_index do |number, i|
    text += ', ' if i > 0
    text += number.to_s
  end
  text
end
```

```ruby
def numbers_text(numbers)
  numbers.join(', ') # [1, 2, 3] => "1, 2, 3"
end
```


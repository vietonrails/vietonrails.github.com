---
layout: post
title: "Giới thiệu 1 số method hay trong Rails"
description: ""
category: Rails
tags: [try method, blank method, present method, presence method, pluck]
comments: true
---

Bài viết này giới thiệu về các method hay và tiện dụng trong ruby và rails như `try`, `blank?`, `present?`, pluck, ...

## Sử dụng Object#try(:method_name) thay vì kiểm tra nil

```ruby
if parent.children && parent.children.singleton?
  singleton = parent.children.first
  send_mail_to(singleton)
end
```

```ruby
# nếu children là nil thì try(:singleton?) cũng trả về nil
# nếu children không nil thì children.singleton? được gọi như bình thường
if parent.children.try(:singleton?)
  singleton = parent.children.first
  send_mail_to(singleton)
end
```
<!-- more -->

## Sử dụng blank?/present?

```ruby
# String
name = nil
name.blank? # => true
name = ""
name.blank? # => true
name = " "
name.blank? # => true
name = "Tom"
name.blank? # => false

# Array
numbers = nil
numbers.blank? # => true
numbers = []
numbers.blank? # => true
numbers = [1, 2, 3]
numbers.blank? # => false

# Hash
params = nil
params.blank? # => true
params = {}
params.blank? # => true
params = { name: "Tom", email: "hoge@hoge.com" }
params.blank? # => false
```

`present?` ngược lại với `blank?`.

```ruby
# String
name = ""
name.present? # => false
name = "Tom"
name.present? # => true
```

## Sử dụng presence

```ruby
if user.name.blank?
  name = "What's your name?"
else
  name = user.name
end
```

```ruby
name = user.name.presence || "What's your name?"
```

`"".presence` hoặc `[].presence` sẽ trả về `nil`.

```ruby
name = ""
puts name.presence || "What's your name?" # => What's your name?
```

Ngoài ra còn có 1 ví dụ rất thú vị về `presence` như sau.

```ruby
# News nếu có ít nhất là 1 news thì gửi mail và tweet
good_news = company.good_news
if good_news.count > 0
  send_mail(good_news)
  tweet(good_news)
end
```

Đoạn mã trên nếu dùng `presence`

```ruby
if good_news = company.good_news.presence
  send_mail(good_news)
  tweet(good_news)
end
```

`company.good_news` trả lại kết quả là 0 thì câu lệnh `company.good_news.presence` sẽ trả về là  `nil`.  Khi đó câu lệnh if sẽ xử lý `false`.

Tương tự như thế,  khi muốn kiểm tra điều kiện "trong trường hợp string có 1 giá trị nào đó".

```ruby
# Nếu name là nil hoặc là string trống （""） thì không hiện message lên
name = blog.user.name
if name.present?
  show_message("Hello, #{name}!")
end
```

```ruby
if name = blog.user.name.presence
  show_message("Hello, #{name}!")
end
```

## Khi kiểm tra sự tồn tại của 1 string thì nên dùng blank? thay vì nil?

Mệnh đề "string không có giá trị" thường không cần phân biệt `nil` và `""`.
Khi sử dụng `nil?` thì lại cho 2 kết quả khác nhau.

```ruby
if email.nil?
  # => nếu email là "" thì vẫn được coi là có nhập dữ liệu và không gọi puts
  puts "Please input email!"
end
```
Đó cũng là lý do nên sử dụng `blank?` hơn.
```ruby
if email.blank?
  # => Nếu email là "" hoặc " " thì xử lý như chưa nhập dữ liệu và gọi puts
  puts "Please input email!"
end
```

Tương tự như thế, khi `validates` trong Model, nếu không có lý do đặc biệt thì nên sử dụng `allow_blank: true`, không nên sử dụng `allow_nil: true`.

## Khi cần filter, nên dùng query thay vì logic
Ruby cung cấp rất nhiều method hay và đơn giản để thao tác với array, nhưng khi cần thực hiện filter trong model của Rails, thì nên sử dụng query để tốc độ xử lý được nhanh hơn.

```ruby
def admin_users
  User.all.select(&:admin?)
end
```

```ruby
def admin_users
  User.where(admin: true)
end
```


## Dùng pluck thay vì map

`pluck` là method để lấy 1 column cho trước trong các record, mà không load toàn bộ các record đó. Vì thế mà tốc độ xử lý và RAM cũng hiệu quả hơn.

```ruby
def admin_user_ids
  User.where(admin: true).map(&:id)
end
```

```ruby
def admin_user_ids
  User.where(admin: true).pluck(:id)
end
```

## Về timezone trong Rails

Trong Rails, có 2 cách để setting timezone, cách 1 là setting trong application.rb, cách 2 là sử dụng timezone dựa theo biến số môi trường TZ.
Nếu trong trường hợp setting giữa 2 cách này mâu thuẫn với nhau, sẽ nảy sinh ra những lỗi không thể dự đoán trước.
Vì thế, tốt hơn là thống nhất chỉ sử dụng timezone trong application.rb.

Ví dụ,  không dùng `Date.today` mà dùng `Date.current`, không dùng `Time.now` mà dùng `Time.current` ( hoặc `Time.zone.now` )

## Các method thời gian hay
```ruby
Date.current # => Tue, 05 Nov 2013

Date.yesterday  # => Tue, 04 Nov 2013
Date.tomorrow # =>  # => Tue, 06 Nov 2013
```

```ruby
Date.current # => 2013-11-05

2.years.ago   # => 2011-11-05 06:21:40 +0900
2.years.since # => 2015-11-05 06:21:40 +0900

2.months.ago   # => 2013-09-05 06:21:40 +0900
2.months.since # => 2014-01-05 06:21:40 +0900
```

Weeks, days, hours, minutes, seconds cũng thế.

Ngoài ra còn rất nhiều cách viết khác nhau để lấy giá trị ngày tháng đặc biệt.

```ruby
date = Date.current # => 2013-11-05

date.yesterday # => 2013-11-04
date.tomoroow  # => 2013-11-06

date.prev_day # => 2013-11-04
date.next_day # => 2013-11-06

date.prev_day(2) # => 2013-11-03
date.next_day(2) # => 2013-11-07

date - 2.days # => 2013-11-03
date + 2.days # => 2013-11-07

date.ago(2.days)   # => 2013-11-03
date.since(2.days) # => 2013-11-07

date.prev_month # => 2013-10-05
date.next_month # => 2013-12-05

date.prev_month(2) # => 2013-09-05
date.next_month(2) # => 2014-01-05

date - 2.months # => 2013-09-05
date + 2.months # => 2014-01-05

date.months_ago(2)   # => 2013-09-05
date.months_since(2) # => 2014-01-05

date.ago(2.months)   # => 2013-09-05
date.since(2.months) # => 2014-01-05
```

Week, year cũng thế.

```ruby
date = Date.current # => 2013-11-05

date.beginning_of_month # => 2013-11-01
date.end_of_month       # => 2013-11-30

date.beginning_of_day # => 2013-11-05 00:00:00 +0900
date.end_of_day       # => 2013-11-05 23:59:59 +0900

datetime = Time.current # => 2013-11-05T06:43:53+09:00

datetime.beginning_of_hour # => 2013-11-05T06:00:00+09:00
datetime.end_of_hour       # => 2013-11-05T06:59:59+09:00
```

```ruby
date = Date.current # => 2013-11-05
date.tuesday?     # => true

date.prev_week(:monday) # => 2013-10-28
date.next_week(:monday) # => 2013-11-11
```

## Các method thay đổi string thành số nhiều, số ít, ...
```ruby
"my_book".camelize # => "MyBook"

"MyBook".underscore # => "my_book"

"my_book".dasherize # => "my-book"

"book".pluralize            # => "books"
"person".pluralize          # => "people"
"fish".pluralize            # => "fish"
"book_and_person".pluralize # => "book_and_people"
"book and person".pluralize # => "book and people"
"BookAndPerson".pluralize   # => "BookAndPeople"

"books".singularize            # => "book"
"people".singularize           # => "person"
"books_and_people".singularize # => "books_and_person"
"books and people".singularize # => "books and person"
"BooksAndPeople".singularize   # => "BooksAndPerson"

"my_books".humanize # => "My books"

"my_books".titleize # => "My Books"

"my_book".classify  # => "MyBook"
"my_books".classify # => "MyBook"

"my_book".tableize # => "my_books"
"MyBook".tableize  # => "my_books"
```

## Squish xoá các space không cần thiết

```ruby
"    My    \r\n  \t   \n   books       ".squish # => "My books"
```

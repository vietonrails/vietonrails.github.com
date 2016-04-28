---
layout: post
title: "Tổng hợp về rails g migration"
description: ""
category: Rails
tags: [migration]
comments: true
---

Khi cần thay đổi DB schema trong Rails, cách làm thông thường nhất là sử dụng command `rails generate migration`, nhưng có lẽ hầu hết mọi người vẫn chưa phát huy hết sự tiện lợi của command này. Bài viết này sẽ tổng hợp cách sử dụng command này. 

<!-- more -->

# Câu lệnh cơ bản

```bash
# tạo migration
$ rails generate migration tên_class

# tạo model
$ rails generate model tên_model
```

tên_class thế nào cũng được, nhưng tốt hơn là tạo thói quen đặt tên theo cấu trúc "action + table name". Khi đó, Rails sẽ tạo 1 file `/db/migrate/timestamp_tên_class.rb`. Trong file này ta sẽ thêm các phần để thay đổi schema. 

# Tạo table

```bash
$ rails g model tên_model tên_field:định_dạng:(unique|index) các_thông_số_khác
```

Ví dụ như muốn tạo 1 table users như dưới đây : 

```bash
mysql> desc users;
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| id         | int(11)      | NO   | PRI | NULL    | auto_increment |
| uuid       | varchar(255) | YES  |     | NULL    |                |
| name       | varchar(255) | YES  |     | NULL    |                |
| created_at | datetime     | YES  |     | NULL    |                |
| updated_at | datetime     | YES  |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
```

```bash
$ rails g model User uuid:string:unique name:string
```

`id`, `created_at`, `updated_at` được thêm vào tự động. 

## Định dạng

- string: chuỗi kí tự ngắn
- text: chuỗi kí tự dài 
- integer
- float: số phức
- decimal : số phức (độ chính xác cao hơn)
- datetime
- timestamp: thêm các field `created_at`, `updated_at`
- time
- date
- binary
- boolean

## Thực hành

Đến đây thì rails mới chỉ tạo ra file migration chứ chưa thay đổi DB. Để thay đổi bạn cần phải thực hiện command rake

```bash
# thực hiện
$ rake db:migrate
```

```bash:command
# rollback
$ rake db:rollback
```

```bash:command
# xác nhận
$ rake db:migrate:status

database: hoge_development

 Status   Migration ID    Migration Name
--------------------------------------------------
   up     20140909055128  Create users
   up     20140909055234  Create spots
   up     20140909055735  Create user spots
   up     20140909072813  Change options to user spot
```


# Thay đổi column đã tạo

Trong trường hợp muốn thay đổi column đã có. 

```bash:command
$ rails g migration ChangeColumnToUser
```

```ruby:/db/migrate/20140909095843_change_column_to_user.rb
class ChangeColumnToUser< ActiveRecord::Migration

  # method định nghĩa những thay đổi
  def up
    change_column :users, :uuid, :string, null: false, default: 0
  end

  # method để có thể quay về trạng thái trước đó
  def down
    change_column :users, :uuid, :string, null: true, default: 0
  end
end
```

Ví dụ trên, column uuid trong model User bị thay đổi thành NOT NULL. `up` và `down` là 2 method được sử dụng trước và sau quá trình migration, hay nói cách khác là cách để rollback lại.

## Options

### NULL / NOT NULL

```ruby:/db/migrate/example.rb
# NULL
change_column :table_name, :column_name, :type, null: true

# NOT NULL
change_column :table_name, :column_name, :type, null: false
```

### index

```ruby:/db/migrate/example.rb
change_column :table_name, :column_name, :type, index: true
```

### default

```ruby:/db/migrate/example.rb
change_column :table_name, :column_name, :type, default: "fifo"
```

### length

```ruby:/db/migrate/example.rb
# varchar(12)
change_column :table_name, :column_name, :string, limit: 12
```

### Add/remove column


```bash:command
$ rails g migration AddColumnToUser fifo:string
```

```ruby:/db/migrate/20140909095843_add_column_to_user.rb
class AddColumnToUser < ActiveRecord::Migration
  def change

    # Add
    add_column :users, :fifo, :string

    # Remove
    remove_column :users, :fifo, :string

  # chỉ định vị trí thêm vào
    add_column :users, :fifo, :string, :after => :uuid
  end
end
```

### Add/remove index

Giả sử muốn thêm/xoá index của clumn name trong User

```bash:command
$ rails g migration AddIndexToUser
```

```ruby:/db/migrate/20140909095843_add_index_to_user.rb
class AddIndexToUser < ActiveRecord::Migration
  def change

    # Add
    add_index :users, :name

    # Delete
    remove_index :users, :name

    # Trường hợp tạo index cho nhiều columns
    add_index :users, [:name, :name2]

  end
end
```
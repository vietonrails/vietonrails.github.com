---
layout: post
title: "Design Pattern : 2. Builder"
description: ""
category: Ruby
tags: [design pattern, builder]
comments: true
---

Builder pattern được sử dụng trong những trường hợp sau.

```
* Để tạo ra 1 object thì cần 1 lượng lớn code
* Việc tạo ra 1 object là rất khó
* Có những việc phải check khi cần tạo ra object
```

# Builder pattern là gì

Builder là 1 design pattern được tạo thành từ 3 thành phần :

1. Director : thực hiện các yêu cầu chỉ thông qua interface do Builder định nghĩa
2. Builder : định nghĩa interface cho Director
3. ConcreteBuilder : là thành phần code của Builder

<!-- more -->

# Sample 1
Ví dụ đơn giản như sau : mô phỏng quá trình tạo ra 1 cốc nước đường.
Phần ConcreteBuilder ở đây sẽ tạo ra class nước đường. Class nước đường sẽ có 2 biến số là nước và đường.

```ruby
# SugarWater là ConcreteBuilder
class SugarWater
  attr_accessor :water, :sugar
  def initialize(water, sugar)
    @water = water
    @sugar = sugar
  end
end
```

Tiếp theo là thành phần Builder sẽ tạo ra class SugarWaterBuilder. Interface cho thao tác tạo class này sẽ có 3 method như sau :

1. add_sugar : thêm đường
2. add_water : thêm nước
3. result : trả về nước đường hiện tại

```ruby
# SugarWaterBuilder : Builder tạo nước đường
class SugarWaterBuilder
  def initialize
    @sugar_water = SugarWater.new(0,0)
  end

  def add_sugar(sugar_amount)
    @sugar_water.sugar += sugar_amount
  end

  def add_water(water_amount)
    @sugar_water.water += water_amount
  end

  def result
    @sugar_water
  end
end
```

Cuối cùng là quá trình tạo nước đường (cook), method này sẽ được đặt trong thành phần Director.

```ruby
class Director
  def initialize(builder)
    @builder = builder
  end

  def cook
    @builder.add_water(150)
    @builder.add_sugar(90)
    @builder.add_water(300)
    @builder.add_sugar(35)
  end
end
```

Khi cần tạo nước đường ta sẽ gọi như sau :

```ruby
builder = SugarWaterBuilder.new
director = Director.new(builder)
director.cook
```

Kết quả sẽ như sau :

```ruby
p builder.result
#=> <SugarWater:0x007fc773085bc8 @water=450, @sugar=125>
```

Như ví dụ ở trên thì Builder đảm nhiệm việc "tay chân", Director đảm nhiệm việc quản lý "quá trình". Bằng cách đó ta dễ dàng thay đổi và thao tác cách tạo ra 1 cốc nước đường.

Tổng kết lại sẽ như sau :

```text
* Đề bài : Tạo 1 cốc nước đường
* SugarWater : vai trò ConcreteBuilder, mang method thay đổi thành phần nước, đường.
* SugarWaterBuilder : vai trò Builder, mang interface thêm nước, thêm đường, và trả lại kết quả.
* Director : vai trò nhận yêu cầu và xử lý thông qua builder.
```

# Sample 2

Ví dụ 1 đã có nước đường thì ví dụ 2 ta sẽ thêm vào nước muối.

```ruby
# SaltWater : ConcreteBuilder
class SaltWater
  attr_accessor :water, :salt
  def initialize(water, salt)
    @water = water
    @salt = salt
  end

  # Thêm vào thành phần ( ở đây là muối )
  def add_material(salt_amount)
    @salt += salt_amount
  end
end
```

Tiếp theo ta sẽ sửa lại class nước đường ở trên. Ta cũng sẽ thêm vào 1 class chung là `add_material` để thêm yếu tố đường vào. 

```ruby
# SugarWater : ConcreteBuilder
class SugarWater
  attr_accessor :water, :sugar
  def initialize(water, sugar)
    @water = water
    @sugar = sugar
  end

  # Thêm vào thành phần ( ở đây là đường )
  def add_material(sugar_amount)
    @sugar += sugar_amount
  end
end
```

Tiếp theo sẽ là Builder. Builder sẽ có 2 điểm thay đổi sau :

1. Sửa tên class Builder thành WaterWithMaterialBuilder
2. method thêm thành phần (muối, đường) sẽ đổi tên thành `add_material`

```ruby
class WaterWithMaterialBuilder
  def initialize(class_name)
    @water_with_material = class_name.new(0,0)
  end

  # thêm các thành phần khác (muối, đường)
  def add_material(material_amount)
    @water_with_material.add_material(material_amount)
  end

  # thêm nước
  def add_water(water_amount)
    @water_with_material.water += water_amount
  end

  # trả lại kết quả
  def result
    @water_with_material
  end
end
```

Cuối cùng là Director. Ở đây method `add_sugar` cũng sửa thành `add_material`.

```
class Director
  def initialize(builder)
    @builder = builder
  end
  def cook
    @builder.add_water(150)
    @builder.add_material(90)
    @builder.add_water(300)
    @builder.add_material(35)
  end
end
```

Giờ ta sẽ xác nhận lại kết quả của đoạn code trên. Đầu tiên là tạo nước đường. kết quả sẽ trả về giống như sample 1. 

```ruby
builder = WaterWithMaterialBuilder.new(SugarWater)
director = Director.new(builder)
director.cook

p builder.result
#=> #<SugarWater:0x007fc773085bc8 @water=450, @sugar=125>
```

Tiếp theo là tạo nước muối.

```
builder = WaterWithMaterialBuilder.new(SaltWater)
director = Director.new(builder)
director.cook

p builder.result
# #<SaltWater:0x007f92cc103ba8 @water=450, @salt=125>
```

Tổng kết lại sẽ như sau :

```text
* Đề bài : Tạo 1 cốc nước với thành phần khác (muối, đường)
* SugarWater, SaltWater : vai trò ConcreteBuilder, mang method thay đổi thành phần nước, thành phần phụ. ConcreteBuilder có thể có nhiều.
* WaterWithMaterialBuilder : vai trò Builder, mang interface thêm nước, thêm thành phần phụ, và trả lại kết quả. Builder chỉ có duy nhất.
* Director : vai trò nhận yêu cầu và xử lý thông qua builder.  Director chỉ có duy nhất. 
```

---
layout: post
title: "Design Pattern: 1. Abstract Factory"
description: ""
category: Ruby
tags: [design pattern, abstract factory]
comments: true
---
Abstract Factory là pattern có nhiệm vụ tạo ra các object và đảm bảo các object này không mâu thuẫn với nhau.

## Ví dụ và code

Để giải thích về Abstract Factory ta lấy ví dụ về 1 cái hồ như sau :

```
* Các class động vật
  * Class Duck có method là eat
  * Class Frog có method là eat

* Các class thực vật
  * Class Algae có method là grow
  * Class WaterLily có method là grow

* Các class tạo hệ sinh thái của hồ
  * Định nghĩa động vật, thực vật bằng constructor
  * Có method trả về class động vật, thực vật

* Hệ sinh thái của hồ (mối quan hệ giữa động vật và thực vật) có 2 quy ước :
  * Duck và WaterLily
  * Frog và Algae
```

Tiếp theo ta sẽ viết code để thoả mãn điều kiện trên.

<!-- more -->

Class Duck và Frog sẽ như sau:

```ruby
class Duck
  def initialize(name)
    @name = name
  end

  def eat
    puts "Duck #{@name} eating"
  end
end

class Frog
  def initialize(name)
    @name = name
  end

  def eat
    puts "Frog #{@name} eating"
  end
end
```

Tiếp theo là class thực vật Algae và WaterLily

```ruby
class Algae
  def initialize(name)
    @name = name
  end

  def grow
    puts "Algae #{@name} growing"
  end
end

class WaterLily
  def initialize(name)
    @name = name
  end

  def grow
    puts "WaterLily #{@name} growing"
  end
end
```

Tiếp theo, trước khi tạo ra hồ ta phải nghĩ về các quy ước về môi trường trong hồ.

```
* Hệ sinh thái của hồ (mối quan hệ giữa động vật và thực vật) có 2 quy ước :
  * Duck và WaterLily
  * Frog và Algae
```

Để bảo vệ các quy ước này - hay nói cách khác là tạo ra các object không mâu thuẫn với nhau - ta sẽ sử dụng Abstract Factory pattern. Trong ví dụ này, để đảm bảo tạo ra những hệ sinh thái trong hồ, ta sẽ sử dụng 2 class sau.

```
* Tạo ra Frog và Algae => FrogAndAlgaeFactory
* Tạo ra Duck và WaterLily => DuckAndWaterLilyFactory
```

Tiếp theo, dựa trên 2 class này, ta sẽ  viết thêm class OrganismFactory để tạo ra hệ sinh thái hồ.

```ruby
# Tạo ra hệ sinh thái hồ (Abstract Factory)
class OrganismFactory
  def initialize(number_animals, number_plants)
    @animals = []
    # Tạo ra động vật trong hồ
    number_animals.times do |i|
      animal = new_animal("Animal #{i}")
      @animals << animal
    end

    @plants = []
    # Tạo ra thực vật trong hồ
    number_plants.times do |i|
      plant = new_plant("Plant #{i}")
      @plants << plant
    end
  end

  # Trả về object thực vật
  def get_plants
    @plants
  end

  # Trả về object động vật
  def get_animals
    @animals
  end
end

# Tạo ra Frog và Algae (Concrete Factory)
class FrogAndAlgaeFactory < OrganismFactory
  private

  def new_animal(name)
    Frog.new(name)
  end

  def new_plant(name)
    Algae.new(name)
  end
end

# Tạo ra Duck và WaterLily (Concrete Factory)
class DuckAndWaterLilyFactory < OrganismFactory
  private

  def new_animal(name)
    Duck.new(name)
  end

  def new_plant(name)
    WaterLily.new(name)
  end
end
```

Giờ hãy chạy thử code trên xem sao :D

```ruby
factory = FrogAndAlgaeFactory.new(4,1)
animals = factory.get_animals
animals.each { |animal| animal.eat }
#=> Frog 0 eating
#=> Frog 1 eating
#=> Frog 2 eating
#=> Frog 3 eating
plants = factory.get_plants
plants.each { |plant| plant.grow }
#=> Algae 0 growing

factory = DuckAndWaterLilyFactory.new(3,2)
animals = factory.get_animals
animals.each { |animal| animal.eat }
#=> Duck 0 eating
#=> Duck 1 eating
#=> Duck 2 eating
plants = factory.get_plants
plants.each { |plant| plant.grow }
#=> WaterLily 0 growing
#=> WaterLily 1 growing
```

Như vậy ta đã có thể tạo ra những hệ sinh thái hồ mà không bị mâu thuẫn với quy ước ban đầu.

## Cấu trúc của Abstract Factory

Abstract Factory gồm có 3 phần quan trọng sau :

```
AbstractFactory: Thực hiện xử lý các phần chung của ConcreteFactory(trong ví dụ này là Pond)
ConcreteFactory: Thực hiện việc sinh ra các object thực tế
(trong ví dụ này là 2 class FrogAndAlgaeFactory và DuckAndWaterLilyFactory)
Product: Là những object được sinh ra từ ConcreteFactory
(trong ví dụ này là Duck， Frog, WaterLily, Algae)
```

## Ưu điểm của Abstract Factory

```
* Tạo ra 1 nhóm các objects có liên quan tới nhau
* Tạo ra 1 nhóm các objects thoả mãn những điều kiện phức tạp
```

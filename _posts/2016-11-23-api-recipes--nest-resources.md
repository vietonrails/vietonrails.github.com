---
layout: post
title: "API recipes : Nest resources"
description: ""
category: Rails
tags: [API, serializer, nest]
comments: true
author: rockkhuya
---

Nest resources là 1 case mà ta sẽ gặp trong hầu hết các dự án. Tuy nhiên, để code sạch đẹp, dễ đọc, dễ hiểu thì không phải ai cũng làm được. 

Recipe này sẽ dùng 1 ví dụ kinh điển để minh hoạ. Mô tả ví dụ như sau : 

- Ta có đối tượng shop có đầy đủ RESTful
- shop có comment, các comment cũng có đầy đủ RESTful

<!-- more -->

## routes

Với mô tả trên, ta sẽ có `routes.rb` như sau : 

```ruby
  resources :shops do
    resources :comments
  end
```

Kiểm tra routes xem thế nào : 

```bash
rails routes
       Prefix Verb   URI Pattern                            Controller#Action
shop_comments GET    /shops/:shop_id/comments(.:format)     comments#index
              POST   /shops/:shop_id/comments(.:format)     comments#create
 shop_comment GET    /shops/:shop_id/comments/:id(.:format) comments#show
              PATCH  /shops/:shop_id/comments/:id(.:format) comments#update
              PUT    /shops/:shop_id/comments/:id(.:format) comments#update
              DELETE /shops/:shop_id/comments/:id(.:format) comments#destroy
        shops GET    /shops(.:format)                       shops#index
              POST   /shops(.:format)                       shops#create
         shop GET    /shops/:id(.:format)                   shops#show
              PATCH  /shops/:id(.:format)                   shops#update
              PUT    /shops/:id(.:format)                   shops#update
              DELETE /shops/:id(.:format)                   shops#destroy
```

Tất cả các actions của `comments` đều được đặt dưới URL là `/shops/:id/`. 
Cách viết này thể hiện mối quan hệ giữa các resources rất rõ ràng : comments phải thuộc 1 và chỉ 1 shop. 
Tuy nhiên nhược điểm là làm URL dài hơn. 
Vì thế mà không nên lạm dụng, chỉ nên dùng nest resources ở 1 level. Xuống đến 2 level là bạn có thể nhận đủ gạch đá xây nhà từ Frontend hoặc mobiles engineer rồi đó :v . 

## models

Phần model khá đơn giản, chỉ có các relationship giữa các model với nhau mà thôi. 

```ruby
class Shop < ApplicationRecord
  has_many :comments
end

class Comment < ApplicationRecord
  belongs_to :shop
end
```

## Controllers

Khi tạo 1 resources mới, mình khá thích dùng `rails g scaffold`. `scaffold` sẽ gen ra cho mình gần như đầy đủ tất cả các file liên quan, ngoài ra code của scaffold cũng rất clear.

```ruby
# app/controllers/shops_controller.rb
class ShopsController < ApplicationController
  before_action :set_shop, only: [:show, :update, :destroy]

  # GET /shops
  def index
    @shops = Shop.all

    render json: @shops
  end

  # GET /shops/1
  def show
    render json: @shop
  end

  # POST /shops
  def create
    @shop = Shop.new(shop_params)

    if @shop.save
      render json: @shop, status: :created, location: @shop
    else
      render json: @shop.errors, status: :unprocessable_entity
    end
  end

  # PATCH/PUT /shops/1
  def update
    if @shop.update(shop_params)
      render json: @shop
    else
      render json: @shop.errors, status: :unprocessable_entity
    end
  end

  # DELETE /shops/1
  def destroy
    @shop.destroy
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_shop
      @shop = Shop.find(params[:id])
    end

    # Only allow a trusted parameter "white list" through.
    def shop_params
      params.require(:shop).permit(:name, :description)
    end
end
```

Tiếp theo là controller của `comments`. Controller này được sửa lại khác nhiều so với default của `scaffold`.

```ruby
class CommentsController < ApplicationController
  before_action :set_shop
  before_action :set_comment, only: [:show, :update, :destroy]

  # GET shops/1/comments
  def index
    @comments = @shop.comments

    render json: @comments
  end

  # GET shops/1/comments/1
  def show
    render json: @comment
  end

  # POST shops/1/comments
  def create
    @comment = @shop.comments.new(comment_params)

    if @comment.save
      render json: @comment, status: :created, location: shop_comment_url(@shop, @comment)
    else
      render json: @comment.errors, status: :unprocessable_entity
    end
  end

  # PATCH/PUT shops/1/comments/1
  def update
    if @comment.update(comment_params)
      render json: @comment
    else
      render json: @comment.errors, status: :unprocessable_entity
    end
  end

  # DELETE shops/1/comments/1
  def destroy
    @comment.destroy
  end

  private
    # Use callbacks 
    def set_shop
      @shop = Shop.find(params[:shop_id])
    end

    # Use callbacks to share common setup or constraints between actions.
    def set_comment
      @comment = @shop.comments.find(params[:id])
    end

    # Only allow a trusted parameter "white list" through.
    def comment_params
      params.require(:comment).permit(:content, :shop_id)
    end
end
```

### Thêm `before_action :set_shop`
Như phần trước đã giải thích, tất cả các `comments` đều phải thuộc 1 và chỉ 1 `shop` duy nhất. Do đó trước tất cả các actions của `comment` đều cần xác định `shop` của nó. 

```ruby
    # Use callbacks 
    def set_shop
      @shop = Shop.find(params[:shop_id])
    end
```

### Sửa lại `set_comment`
Tương tự như lý do trên, ta sẽ sửa lại phần `set_comment`.

```ruby
    def set_comment
      # before : @comment = Comment.find(params[:id])
      @comment = @shop.comments.find(params[:id])
    end
```

Ngoài ra còn 1 số chỗ sửa nhỏ nhỏ khác : 

```ruby
    # @comments = Comment.all
    @comments = @shop.comments

    # @comment = Comment.new(comment_params)
    @comment = @shop.comments.new(comment_params)
```

## Serializers

Serializer thì không có gì phức tạp. 

```ruby
class ShopSerializer < ActiveModel::Serializer
  attributes :id, :name
  has_many :comments
end

class CommentSerializer < ActiveModel::Serializer
  attributes :id, :content
end
```

---
layout: post
title: "Cách sử dụng cells để tối ưu code"
description: ""
category: Rails
tags: [ cells, tối ưu code ]
comments: true
---
# Cells là gì

Khi làm việc với view của Rails, không ngày nào mà không sử dụng tới method partial. Tuy nhiên, partial chỉ là 1 tính năng để gộp và tái sử dụng code view nhiều lần mà thôi. Khi muốn gộp chung cả logic vào để tạo ra template thì vấn đề sẽ rất phức tạp.

Khi gặp vấn đề này, cells sẽ phát huy tối đa sức mạnh của mình.

cells là 1 gem được sử dụng để gộp cả view lẫn controller lại thành 1 package, với những view có logic phức tạp, thì cells sẽ giúp code trở nên dễ đọc và dễ dàng tái sử dụng hơn.

<!-- more -->

# Những trường hợp dùng cells

* hiển thị popup login
* hiển thị ranking ở side menu
* ...

## Ví dụ với popup login

Khi sử dụng partial, ta thường viết như sau :

```haml
#!dashboard.html.haml
- unless user_signed_in?
  .header
    .header__login-button
      = link_to 'login', 'javascript:void(0);'
= render partial: 'popup_login'
```

```
#!popup_login.html.haml
- unless user_signed_in?
  .popup-login
    = form_for(@user, url:user_session_path) do |f|
      %dl
        %dt= f.label :email
        %dd= f.email_field :email
        %dt= f.label :password
        %dd= f.password_field :password
      = f.submit 'Login'
   :javascript
     $('.header__login-button a').click(function(){
       $('.popup-login').css({ 'display' : 'block'});
     });
```

Vấn đề của cách viết này là trong `_popup_login.html.haml` có sử dụng `@user`, và `@user` này được tạo ra ở đâu. Nếu trong controller không định nghĩa `@user` thì đoạn code trên chắc chắn bị lỗi.
Có 2 cách giải quyết ở đây :
1. định nghĩa @user ở trong tất cả các action có gọi popup_login.
2. viết trong application controller

## Các viết trong application_controller

Viết 1 cách đơn giản như sau :

```
class ApplicationController < ActionController::Base
  before_action: :pre_load

  def pre_load
    @user = User.new unless current_user.present?
  end
end
```

Lần này chỉ là popup_login, nên logic còn khá đơn giản. Nếu mọi thứ phức tạp hơn như ranking, comment, ... thì code chắc chắn sẽ không ngắn gọn và dễ hiểu như thế.

## Viết bằng cells


```haml
#!dashboard.html.haml
= render_cell :popup_login, :show
```

Login
Cấu trúc thư mục của cells sẽ như sau :

```
/app
  /assets
  /cells
     /popup_login
       show.html.haml
     popup_login_cell.rb
```

Các đoạn mã để thực hiện chức năng popup sẽ như sau :

```ruby
#!app/cells/popup_login_cell.rb
class PopupLoginCell < Cell::Rails
  def show
    @user = User.new unless current_user.present?
    render
  end
end
```

```haml
#!app/cells/popup_login/show.html.haml
- unless user_signed_in?
  .popup-login
    = form_for(@user, url:user_session_path) do |f|
      %dl
        %dt= f.label :email
        %dd= f.email_field :email
        %dt= f.label :password
        %dd= f.password_field :password
      = f.submit 'Login'
   :javascript
     $('.header__login-button a').click(function(){
       $('.popup-login').css({ 'display' : 'block'});
     });
```

# Sử dụng cells

Thêm vào Gemfile

```
gem 'cells'
```

cells được cung cấp thông qua generate :

```
rails generate cell popup_login show -e haml
  create  app/cells/
  create  app/cells/popup_login
  create  app/cells/popup_login_cell.rb
  create  app/cells/popup_login/show.html.haml
  create  test/cells/popup_login_cell.rb
```

Khi gọi 1 package trong cells :

```
= render_cell :popup_login, :show, :property => @property_name
# có thể truyền tham số cho các package của cells
```

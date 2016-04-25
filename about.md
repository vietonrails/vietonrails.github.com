---
layout: page
title: About
---

<p class="message">
  Sau nhiều comment là tại sao site về Ruby lại sử dụng Wordpress :D, mình đã build lại Việt on Rails bằng jekyll, hàng Ruby 100% nhé.
  Đùa chút chứ 1 phần là do credit card của mình có vấn đề, nên không thanh toán được cho bên host :D. Nhân tiện việc chuyển host nên quyết định dùng Github Pages, vừa miễn phí, vừa nhanh, lại theo xu thế của thời đại, hehe.
</p>

Việt on Rails là cộng đồng những người sử dụng Ruby, Rails và thích trao đổi thêm về nhiều vấn đề khác. Những điều này sẽ giúp từng thành viên chia sẻ và nâng cao trình độ của bản thân :).

Việt on Rails là phi lợi nhuận, tạo ra vì cộng đồng và do cộng đồng để tồn tại.

# Đóng góp bài viết

Các bước để có thể viết bài cho Việt on Rails.

1. clone repo https://github.com/vietonrails/vietonrails.github.com
2. `bundle install` để cài các gems cần thiết
3. Mình đã viết (copy và chỉnh sửa)  thêm 1 số task rake liên quan để thao tác tiện hơn.
4. Tạo bài viết mới rồi gửi pull request
5. Ăn chơi nhảy múa :D

Create new post :

```
rake post title="Awesome title" [date="2012-02-09"] [tags=[tag1,tag2]] [category="category"]
```

Create new page:

```
rake page title="Awesome title"
```

Preview trên local:

```
rake preview
```

Cụ thể và đầy đủ hơn các bạn có thể tham khảo trong README của repo :).

Thân ái và quyết thắng :D.

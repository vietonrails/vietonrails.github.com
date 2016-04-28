Cách cài jekyll và viết bài cho Việt on Rails
===

# Jekyll
https://jekyllrb.com/

jekyll là 1 tool sinh ra các file static HTML từ file markdown. Jekyll được viết bằng Ruby và dành cho dân IT thôi, không phải IT thì cứ wordpress cho nhanh :D.

Nói 1 cách đơn giản về jekyll thì mình sẽ làm như sau :

1. install jekyll
2. tạo 1 trang web mới
3. config và design
4. push lên github
5. viết bài bằng markdown
6. rồi lại push lên github

Về cài đặt jekyll thì tham khảo trang chủ của jekyll nhé. Đơn giản thôi :D.

# Viết bài

1. clone repo này về
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

# Các tasks còn lại
1. Table of contents
2. Chuyển bài viết từ wordpress về jekyll
3. Share button
4. Author


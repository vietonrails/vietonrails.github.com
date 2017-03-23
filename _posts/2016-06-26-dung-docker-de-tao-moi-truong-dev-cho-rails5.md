---
layout: post
title: "Dùng docker để tạo môi trường dev cho Rails5"
description: ""
category: server
tags: [docker, rails5]
comments: true
author: rockkhuya
---
* TOC
{:toc}

## Docker compose
Trong docker có 1 nguyên tắc là 1 image chỉ chứa 1 container. Đây là cách để thao tác đơn giản và dễ dàng lắp ghép với nhau hơn. Tuy nhiên, nếu yêu cầu 1 hệ thống gồm nhiều containers phức tạp và liên kết chặt chẽ với nhau vê thứ tự, config, ... thì docker là không đủ. Và thế là chúng ta có anh chàng `docker-compose`. 

`docker-compose` cũng dùng file YML để định nghĩa, nên việc đọc hiểu là rất đơn giản. 

<!-- more -->

### Ví dụ

[Bài viết về cách dùng compose để build môi trường cho Rails](https://docs.docker.com/compose/rails/)

Chi tiết bạn có thể tham khảo bài viết trên, mình sẽ chỉ tóm tắt lại những ý chính và chỉnh sửa 1 chút để chạy Rails5. 

### Tạo Dockerfile
Bước đầu tiên với docker luôn là tạo images và các container cần thiết. 

Trong ví dụ trên, Dockerfile được định nghĩa đơn giản như sau : 

```
FROM ruby:2.2.0
RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs
RUN mkdir /myapp
WORKDIR /myapp
ADD Gemfile /myapp/Gemfile
ADD Gemfile.lock /myapp/Gemfile.lock
RUN bundle install
ADD . /myapp
```

Dockerfile trên khá dễ hiểu : 
* Chọn base images là ruby 2.2.0 và cài lên container
* Run câu lệnh `apt-get update` các thành phần liên quan
* Tạo thư mục `/myapp` và chuyển vào thư mục đó
* Copy Gemfile và Gemfile.lock vào thư mục
* Run `bundle install`

### Add `Gemfile`
Tiếp theo là thêm `Gemfile` vào. Mình thử `Gemfile` cho Rails 5 như sau : 

```
source 'https://rubygems.org'

gem 'rails', '>= 5.0.0.racecar1', '< 5.1'
# Use mysql as the database for Active Record
gem 'mysql2', '>= 0.3.18', '< 0.5'
# Use Puma as the app server
gem 'puma', '~> 3.0'
# Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
gem 'jbuilder', '~> 2.0'
# Use Redis adapter to run Action Cable in production
# gem 'redis', '~> 3.0'
# Use ActiveModel has_secure_password
# gem 'bcrypt', '~> 3.1.7'

# Use Capistrano for deployment
# gem 'capistrano-rails', group: :development

# Use Rack CORS for handling Cross-Origin Resource Sharing (CORS), making cross-origin AJAX possible
# gem 'rack-cors'

group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platform: :mri
  gem 'better_errors'
  gem 'binding_of_caller'
end

group :development do
  gem 'listen', '~> 3.0.5'
  # Spring speeds up development by keeping your application running in the background. Read more: https://github.com/rails/spring
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

### Add `Gemfile.lock`
File `Gemfile.lock` phải để trống. 

### File `docker-compose.yml`
Đây là file quan trọng nhất chứa đựng toàn bộ magic của compose :D.

```
version: '2'
services:
  db:
    image: postgres
  web:
    build: .
    command: bundle exec rails s -p 3000 -b '0.0.0.0'
    volumes:
      - .:/myapp
    ports:
      - "3000:3000"
    depends_on:
      - db
```

File `docker-compose.yml` này có mục đích như sau : 
* Miêu tả các services cần cho app (1 database và 1 web app). Ở đây là `db` và `web`.
* Cách thức để các images của các services hoạt động với nhau (database thì chạy từ 1 image của postgresql còn web app thì chạy từ thư mục hiện tại bằng `build`).
* Các config cần thiết
* Port của web app 

### Hello, Rails
Với các file và config như trên, ta đã có thể chạy được 1 rails app đơn giản bằng command `docker-compose run`.

```
$ docker-compose run web rails new . --force --database=postgresql --skip-bundle
```

Đầu tiên, `compose` sẽ build 1 image cho `web` service bằng file `Dockerfile`. Sau đó command `rails new` sẽ được thực hiện bên trong container của image. Sau khi kết thúc, ta sẽ nhận được 1 web app đơn giản nhất. 

Nếu đang dùng docker trên Linux, các file được tạo ra từ `rails new` sẽ thuộc quyền `root`. Lý do vì container thực hiện mọi thứ bằng quyền `root`. Nếu cần thì ta có thể đổi lại quyền cho các file này. 

```
 sudo chown -R $USER:$USER .
```

Nếu chạy trên Mac hoặc Windows thì ta không cần quan tâm tới việc này. 

### Connect với database
Default của Rails là chạy db trên `localhost` vì thế ta cần chỉnh sửa lại file `config/database.yml`. 

```
development: &default
  adapter: postgresql
  encoding: unicode
  database: postgres
  pool: 5
  username: postgres
  password:
  host: db

test:
  <<: *default
  database: myapp_test
```

Đến đây đã xong quá trình config và build. Giờ ta sẽ khởi động các containers lên để xem kết quả : 

```
docker-compose up
```

Nếu không có vấn đề gì thì ta sẽ có được log như sau : 

```
docker_db_1 is up-to-date
Creating docker_web_1
Attaching to docker_db_1, docker_web_1
db_1   | The files belonging to this database system will be owned by user "postgres".
db_1   | This user must also own the server process.
db_1   |
db_1   | The database cluster will be initialized with locale "en_US.utf8".
db_1   | The default database encoding has accordingly been set to "UTF8".
db_1   | The default text search configuration will be set to "english".
db_1   |
db_1   | Data page checksums are disabled.
db_1   |
db_1   | fixing permissions on existing directory /var/lib/postgresql/data ... ok
db_1   | creating subdirectories ... ok
db_1   | selecting default max_connections ... 100
db_1   | selecting default shared_buffers ... 128MB
db_1   | selecting dynamic shared memory implementation ... posix
db_1   | creating configuration files ... ok
db_1   | creating template1 database in /var/lib/postgresql/data/base/1 ... ok
db_1   | initializing pg_authid ... ok
db_1   | initializing dependencies ... ok
db_1   | creating system views ... ok
db_1   | loading system objects' descriptions ... ok
db_1   | creating collations ... ok
db_1   | creating conversions ... ok
db_1   | creating dictionaries ... ok
db_1   | setting privileges on built-in objects ... ok
db_1   | creating information schema ... ok
db_1   | loading PL/pgSQL server-side language ... ok
db_1   | vacuuming database template1 ... ok
db_1   | copying template1 to template0 ... ok
db_1   | copying template1 to postgres ... ok
db_1   | syncing data to disk ... ok
db_1   |
db_1   | Success. You can now start the database server using:
db_1   |
db_1   |     pg_ctl -D /var/lib/postgresql/data -l logfile start
db_1   |
db_1   |
db_1   | WARNING: enabling "trust" authentication for local connections
db_1   | You can change this by editing pg_hba.conf or using the option -A, or
db_1   | --auth-local and --auth-host, the next time you run initdb.
db_1   | ****************************************************
db_1   | WARNING: No password has been set for the database.
db_1   |          This will allow anyone with access to the
db_1   |          Postgres port to access your database. In
db_1   |          Docker's default configuration, this is
db_1   |          effectively any other container on the same
db_1   |          system.
db_1   |
db_1   |          Use "-e POSTGRES_PASSWORD=password" to set
db_1   |          it in "docker run".
db_1   | ****************************************************
db_1   | waiting for server to start....LOG:  database system was shut down at 2016-06-26 07:53:53 UTC
db_1   | LOG:  MultiXact member wraparound protections are now enabled
db_1   | LOG:  database system is ready to accept connections
db_1   | LOG:  autovacuum launcher started
db_1   |  done
db_1   | server started
db_1   | ALTER ROLE
db_1   |
db_1   |
db_1   | /docker-entrypoint.sh: ignoring /docker-entrypoint-initdb.d/*
db_1   |
db_1   | LOG:  received fast shutdown request
db_1   | LOG:  aborting any active transactions
db_1   | LOG:  autovacuum launcher shutting down
db_1   | LOG:  shutting down
db_1   | waiting for server to shut down....LOG:  database system is shut down
db_1   |  done
db_1   | server stopped
db_1   |
db_1   | PostgreSQL init process complete; ready for start up.
db_1   |
db_1   | LOG:  database system was shut down at 2016-06-26 07:53:55 UTC
db_1   | LOG:  MultiXact member wraparound protections are now enabled
db_1   | LOG:  database system is ready to accept connections
db_1   | LOG:  autovacuum launcher started
web_1  | => Booting Puma
web_1  | => Rails 5.0.0.rc2 application starting in development on http://0.0.0.0:3000
web_1  | => Run `rails server -h` for more startup options
web_1  | Puma starting in single mode...
web_1  | * Version 3.4.0 (ruby 2.3.0-p0), codename: Owl Bowl Brawl
web_1  | * Min threads: 5, max threads: 5
web_1  | * Environment: development
web_1  | * Listening on tcp://0.0.0.0:3000
web_1  | Use Ctrl-C to stop
```

Tiếp theo là mở `localhost:3000` lên xem.

Ops, chẳng có gì cả :D. Hehe, đương nhiên rồi, vì `localhost:3000` kia là của container, đâu phải của máy ta đang chạy đâu. 

Mỗi container khi khởi động sẽ được cung cấp 1 IP để truy cập. Và để kiếm tra IP này thì bạn dùng command sau : 

```
docker-machine ls
```

Kết quả trả về như sau ( với máy mình nhé, với máy các bạn có thể sẽ khác 1 chút )

```
NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
test      -        virtualbox   Running   tcp://192.168.99.101:2376           v1.11.2
```

Như vậy muốn truy cập vào thì ta sẽ dùng link `192.168.99.101:3000` là sẽ thấy màn hình Rails 5. 

### Tạo db
Đến đây ta thấy 1 vấn đề lớn : làm sao để tạo DB ? Vì như giải thích ở trên, server chạy app ở trên container, nên ta không thể dùng command để tạo ra db được. 

Như vậy ta cần phải "chui" vào container và tạo DB. Cách thực hiện khá đơn giản như sau : 

```
$ docker-compose run web rake db:create
```

Đến đây đã xong :D. 

## Sử dụng lại docker để start dự án

Để tái sử dụng lại các file docker này, bạn chỉ cần tạo 1 thư mục và lưu lại 4 files ban đầu chúng ta đã config. Sau đó khi start 1 dự án mới thì copy ra và build. 

Các bạn có thể tham khảo repo sau đây để start 1 dự án nhanh chóng và tiện lợi hơn. 
[VietOnRails-docker-rails](https://github.com/vietonrails/docker-rails)

---
layout: post
title: "Cài đặt môi trường Python"
description: ""
category: Python
tags: [python, anaconda, pyenv]
comments: true
---
* TOC
{:toc}

## Cấu hình
* Mac osx Sierra 10.12.3

Có nhiều cách để cấu hình môi trường Python. Lúc đầu mình định để tất cả trong docker, nhưng vì còn dùng camera, GPU, … nên việc config docker sẽ mất thời gian. Vì thế mà môi trường python sẽ được dùng trực tiếp trên local. 

Mình sẽ dùng `brew`, `pyenv` và `conda` để cấu hình môi trường. 

<!-- more -->

## Install

### Homebrew

`homebrew` gần như là 1 phần không thể thiếu trong môi trường làm việc trên mac os. 

```bash
brew --version
Homebrew 1.1.9
Homebrew/homebrew-core (git revision bb84; last commit 2017-02-06)
```

Nếu chưa có `homebrew` thì có thể dùng command sau để install :

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

 Update homebrew mới nhất

```bash
brew update && brew upgrade
```

### Pyenv
`pyenv` là công cụ để quản lý version Python. Như đã biết, python 2 và python 3 có sự khác biệt rất lớn, việc sử dụng nhiều version là đương nhiên. 

```bash
brew install pyenv 
```

Nếu dùng `bash` thì `~/.bash_profile`, còn `zsh` sẽ là `~/.zshrc`:

```bash
echo 'export PYENV_ROOT="${HOME}/.pyenv"' >> ~/.bash_profile
echo 'export PATH="${PYENV_ROOT}/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(pyenv init -)"' >> ~/.bash_profile
exec $SHELL -l
```

### Python(Anaconda)
Để xem list các version có thể install được từ `pyenv` :

```bash
pyenv install -l
```

Bạn tìm version mới nhất của `anaconda` để install : 

```bash
pyenv install anaconda3-4.2.0
pyenv global anaconda3-4.2.0
```

Xác nhận lại :

```bash
python --version
Python 3.5.2 :: Anaconda 4.2.0 (x86_64)
```

## Các câu lệnh pyenv cơ bản 
Các version python trong máy : 

```bash
pyenv versions
```

Chuyển version python trong máy :

```bash
pyenv global anaconda3-4.2.0
```

List versions python hiện có thể install :

```bash
pyenv install -l
```

Install 1 version python mới : 

```bash
pyenv install [version]
```


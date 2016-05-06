---
layout: post
title: "Fix lỗi khi brew không thể update"
description: ""
category: Configure
tags: [brew update]
comments: true
author: rockkhuya
---

Đang định cài Hugo về chơi mà update brew thì gặp tình trạng này :

```text
brew update
/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/rubygems/core_ext/kernel_require.rb:55:in `require': cannot load such file -- mach (LoadError)
    from /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/rubygems/core_ext/kernel_require.rb:55:in `require'
    from /usr/local/Library/Homebrew/extend/pathname.rb:2:in `<top (required)>'
    from /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/rubygems/core_ext/kernel_require.rb:55:in `require'
    from /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/rubygems/core_ext/kernel_require.rb:55:in `require'
    from /usr/local/Library/Homebrew/global.rb:3:in `<top (required)>'
    from /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/rubygems/core_ext/kernel_require.rb:55:in `require'
    from /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/rubygems/core_ext/kernel_require.rb:55:in `require'
    from /usr/local/Library/brew.rb:15:in `<main>'
```

Lý do thì mình không hiểu tại sao, nhưng có lẽ là do update version của osx lên El Capitan.

Lỗi này chắc những người dùng mac cũng hay gặp, nên mình sẽ share luôn cách mình đã xử lý vấn đề này.

<!-- more -->

## reset lại /usr/local

Lệnh brew không thể sử dụng được nên mình cần phải vào reset và clean lại folder của brew.

```text
cd /usr/local
git reset --hard && git clean -df
```

## change lại quyền của folder /usr/local

Khi sử dụng `brew doctor` thì gặp vấn đề này :

```text
local|master brew doctor
Please note that these warnings are just used to help the Homebrew maintainers
with debugging if you file an issue. If everything you use Homebrew for is
working fine: please don't worry and just ignore them. Thanks!

Warning: The /usr/local directory is not writable.
Even if this directory was writable when you installed Homebrew, other
software may change permissions on this directory. Some versions of the
"InstantOn" component of Airfoil are known to do this.

You should probably change the ownership and permissions of /usr/local
back to your user account.

Warning: You are using OS X 10.11.
We do not provide support for this pre-release version.
You may encounter build failures or other breakage.
```

Trong phần warning thì folder `/usr/local` không có quyền write, nên ta sẽ cấp quyền như sau :

```text
sudo chown -R $(whoami):admin /usr/local
```

`brew doctor` lại thì thấy vấn đề đã được giải quyết.

## Brew update

Cuối cùng thì thử xem `brew update` có hoạt động bình thường không :

```text
brew update

Checking out files: 100% (3749/3749), done.
==> Migrating Homebrew to v0.9.9
remote: Counting objects: 466, done.
remote: Compressing objects: 100% (423/423), done.
remote: Total 466 (delta 26), reused 301 (delta 17), pack-reused 0
Receiving objects: 100% (466/466), 768.34 KiB | 143.00 KiB/s, done.
Resolving deltas: 100% (26/26), done.
From https://github.com/Homebrew/brew
 + 53c5089...b0d906f master     -> origin/master  (forced update)
HEAD is now at b0d906f compilers: support gcc 6
==> Homebrew has enabled anonymous aggregate user behaviour analytics
Read the analytics documentation (and how to opt-out) here:
  https://git.io/brew-analytics
==> Tapping homebrew/core
Cloning into '/usr/local/Library/Taps/homebrew/homebrew-core'...
remote: Counting objects: 3684, done.
remote: Compressing objects: 100% (3571/3571), done.
remote: Total 3684 (delta 14), reused 530 (delta 1), pack-reused 0
Receiving objects: 100% (3684/3684), 2.82 MiB | 298.00 KiB/s, done.
Resolving deltas: 100% (14/14), done.
Checking connectivity... done.
Checking out files: 100% (3687/3687), done.
Tapped 3559 formulae (3,710 files, 8.8M)
Already up-to-date.
```

Đến đây thì vấn đề đã được giải quyết :).



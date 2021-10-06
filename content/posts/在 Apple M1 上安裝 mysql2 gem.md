---
title: "在 Apple M1 上安裝 mysql2 gem"
description: "mysql2 gem 無法安裝的解決方法"
date: 2021-07-04T17:09:25+08:00
draft: false
toc: false
tags:
  - Rails
  - M1
categories:
  - Rails
---

最近新建 rails project 遇到 bundle 到 mysql 會過不了

有一行錯誤訊息是

`ld: library not found for -lzstd`

## 解決方法

找到 `zstd` 的路徑

```bash
brew install zstd # 如果沒安裝過的話要安裝
which zstd
```

最後找到的 `zstd` 的路徑會是像這樣

`/opt/homebrew/Cellar/zstd/1.5.0/lib`

BTW 如果你是從 intel 晶片 Mac 移轉到 M1 Mac 的話，  
兩個晶片版本下的 Homebrew 路徑是不一樣的，最好在移轉後重新安裝一次 Homebrew 並重新安裝所有套件

到專案目錄下

`bundle config --local build.mysql2 "--with-opt-dir="$(brew --prefix openssl) --with-ldflags=-L/opt/homebrew/Cellar/zstd/1.5.0/lib""`

再 `bundle`

就完成安裝 mysql2 gem 了

### 更新

上面的解法的電腦環境是從原本的 intel 晶片的機器移轉到 ARM 晶片的 M1 MacBook Pro  
之後在全新的 M1 上又遇到安裝 mysql gem 的 bug  
主要的差別是 brew 的安裝路徑不一樣，如果是從舊版 Intel 點對點轉資料到 M1 的機器上，可能會有一些潛在的問題

如果可以的話，拿到 ARM 晶片的機器最好是重新安裝而不是點對點轉移。
後來是用另一個解法，在兩台 M1 機器上不同的 project 試過預到安裝問題都成功解決
前提是你的 mysql 是用 brew 安裝

請根據不同的 gem 版本和 mysql 在 homebrew 中的安全路徑調整這個指令，應該可以正常解決(希望)

```bash
gem install mysql2 -v '0.5.3' -- \
--with-mysql-lib=/opt/homebrew/Cellar/mysql/8.0.26/lib \
--with-mysql-dir=/opt/homebrew/Cellar/mysql/8.0.26 \
--with-mysql-config=/opt/homebrew/Cellar/mysql/8.0.26/bin/mysql_config \
--with-mysql-include=/opt/homebrew/Cellar/mysql/8.0.26/include
```

## 參考

https://stackoverflow.com/questions/67840691/ld-library-not-found-for-lzstd-while-bundle-install-for-mysql2-gem-ruby-on-mac

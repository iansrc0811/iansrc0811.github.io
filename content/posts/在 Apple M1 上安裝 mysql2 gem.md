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

最近操新建 rails project 遇到 bundle 到 mysql 會過不了

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

## 參考

https://stackoverflow.com/questions/67840691/ld-library-not-found-for-lzstd-while-bundle-install-for-mysql2-gem-ruby-on-mac

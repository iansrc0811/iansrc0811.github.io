---
title: "在 Docker 安裝 imagemagick"
date: 2023-06-12T16:43:45+08:00
draft: false
tags:
  - Docker
  - imagemagick
categories:
  - Docker
---

在 Docker 安裝 imagemagick

種種因素，找不到適合的 imagemagick, 最後用下載 binary 的方式安裝

參考[官網](https://imagemagick.org/script/install-source.php)

下載 tar 的壓縮檔，解壓縮後，執行 configure, make, make install  
最後再刪掉下載的檔案就完成了

Dockerfile 中安裝 imagemagick 的部分如下。版本可以自行修改

```bash
Run wget -c <https://github.com/ImageMagick/ImageMagick/archive/refs/tags/7.1.1-8.tar.gz> -O - | tar -xz && \
    cd ImageMagick-7.1.1-8 && \
    ./configure && \
    make && make install && \
    ldconfig /usr/local/lib && \
    cd .. && rm -rf ImageMagick-7.1.1-8/
```

裝完發現還會有 `no decode delegate error` 的問題，以致於不能進行圖片的壓縮等操作

所以還需要在安裝套件的步驟中，加入下面的指令

```bash
apt-get update -qq && apt-get install -yq  libpng-dev libjpeg-dev
```

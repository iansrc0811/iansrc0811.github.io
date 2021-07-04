---
title: "使用 Hugo 建製靜態網站"
date: 2021-06-25T10:47:00+08:00
draft: false
subtitle: 介紹一下 Hugo 這個靜態網站框架
tags:
  - HUGO
categories:
  - HUGO
---
終於弄好了自己的 blog~~  
比較幾個靜態網頁的框架，最後選擇了 Hugo  
以下就說明一下怎麼安裝囉

## 安裝

[參考 Hugo 官網](https://gohugo.io/getting-started/quick-start/)

### 安裝只要用 Homebrew 就可以安裝

`brew install hugo`

### 在想要的目錄下建立 Hugo 專案

`hugo new site new_blog`

### 安裝主題

我使用的是 [Meme](https://themes.gohugo.io/hugo-theme-meme/)  
進到剛剛建立的 folder 中 (new_blog)

```bash
git init
git submodule add --depth 1 https://github.com/reuixiy/hugo-theme-meme.git themes/meme
```

補充說明:

有一個主要的設定需要複製到 `themes` 資料夾之外

`rm config.toml && cp themes/meme/config-examples/zh-tw/config.toml config.toml`

其餘較細結設定可參考 [Meme 官網](https://themes.gohugo.io/hugo-theme-meme/)，不同的主題也會有不同的設定方式  
在試這些設定也是滿好玩的

安裝主題這邊可能比較困惑的點是，在 `themes` 資料夾中會有一個 `exampleSite`  
這個可以不必理會

還有一些設定檔也應該要搬到 `themes` 之外的地方做設定，  
只要知道不應該去修改 `themes` 中的設定，設定應該都要在 `themes` 以外的地方

## 發佈新文章

`hugo new "posts/new post.md"`

會產生 `new post` 這個 .md 檔  
內容如下

```marddown
---
title: "My First Post"
date: 2019-03-26T08:47:11+01:00
draft: true
---
```

這個設定是可以客製化的，可以新增很多 key  
詳細可以參考 [Hugo 官網的 front-matter](https://gohugo.io/content-management/front-matter/)  
這個是每個文章的開頭格式，接下來才是自己要寫的內容。

記得 draft 要改成 false 才能在網頁上看得到文章  
但是在 local 開機的時候 draft false 也會顯示

## 在本地開啟

最後只要  
`hugo server -D`  
就可以在瀏覽器打開頁面了
不得不說 Hugo 的編譯速度真的很快💯

## 自定網域

由於可能會想用自己的 domain 而不是 Github Page 預設的網址，

所以會需要在 [Namecheam](https://www.namecheap.com/) 等網站設定 CNAME，  
然後要在專案 root 建立一個名叫 `CNAME` 的檔案  
裡面只需要存網域名 `www.CUSTOMER_DOMAIN.XXX`

但如果跟我一樣用 github action 自動部屬，就可以略過此步驟，見下一段說明

## 自動佈署

這樣的設定讓我只要推 development branch，Github 就會佈署一個版本到 master

會先編好一個靜態資源到 public 資料夾下

`.github/workflows/gh-pages.yml`

```yaml
name: github pages

on:
  push:
    branches:
      - development  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
      #     fetch-depth: 1    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Clean public
        run: rm -rf public/*

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
          publish_branch: master
          force_orphan: true
          cname: 'www.ian-yeh.com' # 佈署時會產生 CNAME 檔
# https://dwye.dev/post/hugo-github-action/
```

## 改 Favicon

![https://i.imgur.com/zdScqd9.png](https://i.imgur.com/zdScqd9.png)

(就是那個小圖示)

找要想要的圖片，然後前往 [https://realfavicongenerator.net/](https://realfavicongenerator.net/) 生成相關圖示和檔案，  
下載後解壓

僅保留

- android-chrome-512x512.png
- apple-touch-icon.png
- mstile-150x150.png
- safari-pinned-tab.svg
- favicon.ico
- site.webmanifest

這些檔案，刪除其他的  
然後將這些檔案移動到 `static/icons/` 目錄下，

再將

- favicon.ico
- site.webmanifest

移動到 `static/` 目錄下，

最後將 site.webmanifest 重新命名為 `manifest.json`

並檢查和修改相關內容

## 參考

[https://gist.github.com/lisez/41cebe4eb9190a5c5e879fee5933beb1](https://www.notion.so/41cebe4eb9190a5c5e879fee5933beb1)  
[https://dwye.dev/post/hugo-github-action/](https://dwye.dev/post/hugo-github-action/)  
[https://kaiiiz.github.io/blog/hugo-with-github-actions/](https://kaiiiz.github.io/blog/hugo-with-github-actions/)

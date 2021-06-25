---
title: "Hugo 建製網站"
date: 2021-06-25T10:47:00+08:00
draft: false
---

[https://gohugo.io/getting-started/quick-start/](https://gohugo.io/getting-started/quick-start/)

`brew install hugo`

`hugo new site ian_blog`

進入目錄 (ian_blog)

在 root 建立 `CNAME` file

裡面存我要的 domain `www.ian-ye.com`

## 自動佈署

這樣的設定讓我只要推 development branch，github 就會佈署一個版本到 master

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
# https://dwye.dev/post/hugo-github-action/
```

## 發佈新文章

`hugo new "posts/new post.md"`

# meme theme

[https://themes.gohugo.io/hugo-theme-meme/](https://themes.gohugo.io/hugo-theme-meme/)

### 自動部署 Github page

參考

[https://gist.github.com/lisez/41cebe4eb9190a5c5e879fee5933beb1](https://www.notion.so/41cebe4eb9190a5c5e879fee5933beb1)

[https://dwye.dev/post/hugo-github-action/](https://dwye.dev/post/hugo-github-action/)

[https://kaiiiz.github.io/blog/hugo-with-github-actions/](https://kaiiiz.github.io/blog/hugo-with-github-actions/)

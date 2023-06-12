---
title: "解決 Rails Console 貼上大量資料會很慢"
date: 2023-06-12T12:25:39+08:00
draft: false
tags:

- Rails
categories:

- Rails
---
結論: `rails console -- --nomultiline`

有時候在 Rails Console 要貼上大量資料時，會發現貼上去的速度很慢

這時候可以這樣解決

```bash
rails console -- --nomultiline
```

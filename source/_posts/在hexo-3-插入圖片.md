title: 在hexo 3 插入圖片
date: 2015-10-20 05:33:40
description: 在hexo 3 利用asset folder 插入圖片遇到的問題
categories: Problem
tags:
- Hexo
---

# 首先條件
hexo 3 支持asset folder
只要把圖片放左post同層的同名字folder, 然用利用asset_img 就能連結到圖片。

文件結構如下圖：
{% asset_img 'show.png' %}

在post 中加入以下程式碼：
```
{% asset_img 'messy.png' %}
{% asset_img 'source.png' %}
```

------

# 遇到的問題
[官方文件](https://hexo.io/docs/asset-folders.html)有少許誤導，post_asset_folder 作用為開新post 時自動開同名folder
但如果_config.yml 中的post_asset_folder 設定為false, 一定會插入圖片失敗。
因為hexo generate 不會把圖片和post 一起放在public的folder內

# 解決方法
在_config.yml 把 post_asset_folder 設定為true

title: pacman首頁顯示混亂
date: 2015-10-20 04:45:28
description: 程式碼在pacman 主題首頁下顯示為一堆不明數字
categories: 經驗分享
tags:
- hexo
- pacman
---

剛安裝了pacman 主題, 可是程式碼在首頁亂作一團...
{% asset_img messy.png %}

------------ 

# 原因
看了template 的source code 才發現首頁會使用一個description 的attribute，否則就會strip_html(item.content)...難怪亂作一團。以前的tutorial 好像很多都叫人使用description 這個attribute，但官方文件卻沒有說明...
{% asset_img source.png %}

# 解決辨法：
1. 在post 的最上加上description
2. 改template
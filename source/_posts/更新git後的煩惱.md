title: 更新git後的煩惱
date: 2015-10-30 05:10:45
description: hexo-deployer-git 的奇怪問題
categories: 經驗分享
tags: 
- hexo
- nightmare
- git
---

# 可惡的更新
今天更新了xcode，連帶更新了git，卻令hexo-deployer-git 失效。
輸入`hexo deploy` 後，一直彈出 `Git - Permission denied (publickey)`

網上不少人都遇到同樣問題，有的把git 降回舊版本(2.0之前)，有的不用ssh 轉用https
一開始還以為是自帶程式庫問題，研究了一番程式碼，但無論怎樣更改結果也是相同。
訊息提到權限問題，我又利用管理員權限sudo，還把ssh 的key 的file permission 轉為777，最後更下載了最新版本的git, 結果還是一樣。


# 想到的解決方法
模仿hexo-deployer-git 的設計理念，寫了一個shell script 方便deploy。我在Mac OS 測試，有些指令Linux 也許不相容。

{% codeblock %}
#!/bin/bash

# The folder to be deployed and Github repository
dest=.deploy_folder
repo=git@github.com:Yourname/Yourname.github.io.git

# Init a new git directory at the first time
# ditto is an utility for merging files
if [ ! -d $dest ]; then
    ditto public $dest
    cd $dest
    git init .
    msg="first commit"
else
    ditto public $dest
    cd $dest
    msg="Site update at: `date`"
fi

# Upload to the server
git add . 
git commit -m "$msg"
git push -u "$repo" "master:master" --force
{% endcodeblock %}

-------





# 最後解決方法

根據[這位大神](http://stackoverflow.com/questions/2643502/git-permission-denied-publickey)的回覆，這個問題是由public key authetication 引起，而原因是上載到server 的public key 內的email 和git global config 的email 不同。這是什麼錯誤訊息orz... 


只需設定global username 同global email (切記要和public key 內的email 相同)
{% codeblock %}
git config --global user.name "Yourname"
git config --global user.email "YourEmail@email.com"
{% endcodeblock %}
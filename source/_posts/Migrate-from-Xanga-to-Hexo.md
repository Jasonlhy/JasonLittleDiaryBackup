title: Migrate from Xanga to Hexo
date: 2016-04-29 01:09:11
description:
categories: Problem
tags:
- Xanga
- Hexo
---

# Download Migration Plugin
{% codeblock %}
npm install hexo-migrator-wordpress --save
{% endcodeblock %}

# Download Archive from Xanga
I got a folder named `29018156_1`, and `29018156_2.xml` inside it stores my xanga content.
{% asset_img 'archive.png' %}

# Modify the Plugin
Since the format of the archive is not compatible with the plugin, modify the source codes just like the pictures shown below.
{% asset_img 'change1.png' %}
{% asset_img 'change2.png' %}

# Do migration
{% codeblock %}
hexo migrate wordpress /Users/Jason/Downloads/29018156_1/29018156_2.xml
{% endcodeblock %}

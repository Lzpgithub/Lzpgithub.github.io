---
layout: page
title: About
description: 
keywords: Lzpgithub
comments: true
menu: 关于
permalink: /about/
---

海阔凭鱼跃

天高任鸟飞

## 坚信

* 科技创造未来
* 编程改变世界

## 联系

* GitHub：[@Lzpgithub](https://github.com/Lzpgithub)
* 博客：[{{ site.title }}]({{ site.url }})
* 微博: [@Arislzp](http://weibo.com/Arislzp)
* QQ邮箱: [@Aris 1317343152@qq.com]()


## Skill Keywords

#### Software Engineer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_software_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>

#### Mobile Developer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_mobile_app_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>

#### Windows Developer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_windows_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>

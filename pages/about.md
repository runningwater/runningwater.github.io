---
layout: page
title: About
description: 
keywords: runningwater
comments: true
menu: 关于
permalink: /about/
---

我不与谁争，与谁争我都不屑。

我爱大自然，其次就是艺术。

我用双手烤着命令之火，火萎了，我也该走了。

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## 技术技能

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}

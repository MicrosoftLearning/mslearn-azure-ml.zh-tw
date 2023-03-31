---
title: 線上託管說明
permalink: index.html
layout: home
---

# Microsoft Learn - 實際操作練習

下列實際操作練習旨在支援 [Microsoft Learn](https://docs.microsoft.com/training/) 訓練。

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %}
| |
| --- | --- | 
{% for activity in labs  %}| [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

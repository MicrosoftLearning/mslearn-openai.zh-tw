---
title: Azure OpenAI 練習
permalink: index.html
layout: home
---

# Azure OpenAI 練習

下列練習旨在支援 [Microsoft Learn](https://learn.microsoft.com/training/browse/?terms=OpenAI) 的課程模組。


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}

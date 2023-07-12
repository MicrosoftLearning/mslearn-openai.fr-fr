---
title: Exercices Azure OpenAI
permalink: index.html
layout: home
---

# Exercices Azure OpenAI

Les exercices suivants sont conçus pour prendre en charge les modules sur [Microsoft Learn](https://learn.microsoft.com/training/browse/?terms=OpenAI).


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}

---
layout: page
title: Portfolio
permalink: /portfolio/
desc: 投資組合&#x30FB;Portfelj&#x30FB;Portefeuille&#x30FB;ポートフォリオ&#x30FB;Salkku
---

{% assign projects = site.projects | sort:"date" | reverse %}
{% for project in projects %}
  <h2><a class="post-link" href="{{ project.url }}">
    {{ project.title }}
  </a></h2>
  <p>{{ project.content | markdownify }}</p>
{% endfor %}

---
layout: default
title: Machine Learning Knowledge Index
permalink: /ML/
---

# 📊 Machine Learning — Blog Index

This section captures my notes on Machine Learning fundamentals, training workflows, model deployment, and applied ML systems.

---

## 📚 Posts

<ul>
{% assign posts = site.categories.ml | sort: "date" %}
{% for post in posts %}
  <li>
    <a href="{{ post.url }}">
      {{ post.date | date: "%Y-%m-%d" }} — {{ post.title }}
    </a>
  </li>
{% endfor %}
</ul>

---

🔄 This index updates automatically as new ML posts are published.

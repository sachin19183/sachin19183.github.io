---
layout: default
title: Golang Knowledge Index
permalink: /GOLANG/
---

# ⚙️ Golang — Knowledge Index

This section documents my learnings on programing in Golang.

---

## 📚 Posts

<ul>
{% assign posts = site.categories.golang | sort: "date" %}
{% for post in posts %}
  <li>
    <a href="{{ post.url }}">
      {{ post.date | date: "%Y-%m-%d" }} — {{ post.title }}
    </a>
  </li>
{% endfor %}
</ul>

---

🔄 This index updates automatically as new golang posts are published.

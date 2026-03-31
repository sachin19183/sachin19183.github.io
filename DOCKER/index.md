---
layout: default
title: Docker Knowledge Index
permalink: /DOCKER/
---

# 🐳 Docker — Knowledge Index

This section documents my learnings on containerization, Docker architecture, image management, networking, and deployment workflows.

---

## 📚 Posts

<ul>
{% assign posts = site.categories.docker | sort: "date" %}
{% for post in posts %}
  <li>
    <a href="{{ post.url }}">
      {{ post.date | date: "%Y-%m-%d" }} — {{ post.title }}
    </a>
  </li>
{% endfor %}
</ul>

---

🔄 This index updates automatically as new Docker posts are published.

---
layout: default
title: GCP Knowledge Index
permalink: /GCP/
---

# ☁️ Google Cloud Platform — Knowledge Index

This section contains my architecture notes, hands-on labs, and certification learnings on Google Cloud Platform.

---

## 📚 Posts

<ul>
{% assign posts = site.categories.GCP | sort: "date" %}
{% for post in posts %}
  <li>
    <a href="{{ post.url }}">
      {{ post.date | date: "%Y-%m-%d" }} — {{ post.title }}
    </a>
  </li>
{% endfor %}
</ul>

---

🔄 This index updates automatically as new GCP posts are published.

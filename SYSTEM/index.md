---
layout: default
title: System Design & Engineering Notes
permalink: /SYSTEM/
---

# ⚙️ System — Knowledge Index

This section captures how I design, structure, and build systems — including this website itself.

It focuses on architecture decisions, debugging journeys, and lessons learned while building real-world systems.

---

## 📚 Posts

<ul>
{% assign posts = site.categories.SYSTEM | sort: "date" | reverse %}
{% for post in posts %}
  <li>
    <a href="{{ post.url }}">
      {{ post.date | date: "%Y-%m-%d" }} - {{ post.title }}
    </a>
  </li>
{% endfor %}
</ul>

---

ℹ️ This index updates automatically as new system-related posts are added.

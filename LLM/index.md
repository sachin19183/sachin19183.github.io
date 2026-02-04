---
layout: default
title: LLM Knowledge Index
permalink: /LLM/
---

# 🤖 Large Language Models — Knowledge Index

This section contains my learnings, experiments, and architecture notes on Large Language Models (LLMs), prompt engineering, and agentic AI systems.

---

## 📚 Posts

<ul>
{% assign posts = site.categories.LLM | sort: "date" %}
{% for post in posts %}
  <li>
    <a href="{{ post.url }}">
      {{ post.date | date: "%Y-%m-%d" }} — {{ post.title }}
    </a>
  </li>
{% endfor %}
</ul>


---

🔄 This index updates automatically as new LLM posts are published.

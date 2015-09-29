---
layout: default
---

<ul>
  {% for post in site.posts limit: 10 %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>
---
layout: home
author_profile: true
---

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
    <li>This is a post</li>
  {% endfor %}
</ul>

---
layout: home
author_profile: true
---

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      <i><font size="3"> {{ post.date | date: "%Y-%m-%d"}} </font></i>
    </li>
  {% endfor %}
</ul>

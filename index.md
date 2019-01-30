---
layout: home
author_profile: true
---

<h1>Home page</h1>
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
    <li>This is a post</li>
  {% endfor %}
</ul>

---
layout: home
author_profile: true
---
<h2>AI</h2>
<ul>
  {% for post in site.posts %}
    {% if post.filter_by == 'ai' %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
    {% endif %}
  {% endfor %}
</ul>

<h2>Rust Programming</h2>
<ul>
  {% for post in site.posts %}
    {% if post.filter_by == 'rust' %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
    {% endif %}
  {% endfor %}
</ul>

<h2>Software Development</h2>
<ul>
  {% for post in site.posts %}
    {% if post.filter_by == 'tech' %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
    {% endif %}
  {% endfor %}
</ul>

<h2>Machine Learning</h2>
<ul>
  {% for post in site.posts %}
    {% if post.filter_by == 'ml' %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
    {% endif %}
  {% endfor %}
</ul>
<!-- <ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      <i><font size="3"> {{ post.date | date: "%Y-%m-%d"}} </font></i>
    </li>
  {% endfor %}
</ul> -->

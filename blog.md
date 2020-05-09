---
layout: default
title: Blog
---
<h1>Latest Posts</h1>

<ul>
  {% for post in site.posts %}
    {% if page.minutes %}
      {% assign minutes = page.minutes %}
    {% else %}
      {% assign minutes = post.content | number_of_words | divided_by: 180 %}
      {% if minutes == 0 %}{% assign minutes = 1 %}{% endif %}
    {% endif %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
        <small> {{ minutes }} minute read ({{ post.content | number_of_words }} words) </small>

      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>

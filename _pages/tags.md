---
layout: default
permalink: /tags
---

{% assign tags_max = 0 %}
{% for tag in site.tags %}
{% if tag[1].size > tags_max %}
{% assign tags_max = tag[1].size %}
{% endif %}
{% endfor %}

<ul>
{% for i in (1..tags_max) reversed %}
{% for tag in site.tags %}
{% if tag[1].size == i %}
<li>
<a href="#{{ tag[0] | slugify }}">
<strong>{{ tag[0] }}</strong> - <span>{{ i }}</span>
</a>
</li>
{% endif %}
{% endfor %}
{% endfor %}
</ul>

{% for i in (1..tags_max) reversed %}
{% for tag in site.tags %}
{% if tag[1].size == i %}
<section id="{{ tag[0] | slugify | downcase }}">
<h2>{{ tag[0] }}</h2>
<div>
{% for post in tag.last %}
* <a href="{{ post.url  }}">{{ post.title }}</a>
{% endfor %}
</div>
<a href="#page-title" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
</section>
{% endif %}
{% endfor %}
{% endfor %}

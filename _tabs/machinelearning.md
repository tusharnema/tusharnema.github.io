---
layout: posts
permalink: /machine-learning/
title: "Machine learning projects "
author_profile: true
header:
  image: "/images/ml.jpg"
---



{% for tag in group_names %}
  {% assign posts = group_items[forloop.index0] %}
  <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2>
  {% for post in posts %}
    {% include archive-single.html %}
  {% endfor %}
{% endfor %}
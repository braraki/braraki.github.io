---
layout: page
title: Research
permalink: /research/
---

{% if site.data.publications.papers %}
  {% for paper in site.data.publications.papers %}
    <a class="page-link" href="{{ link.url | prepend: site.baseurl }}">{{ link.title }}</a>
  {% endfor %}
{% endif %}
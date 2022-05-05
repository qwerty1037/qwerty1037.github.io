---
title: "Record"
layout: archive
permalink: categories/record
---

{% assign posts = site.categories.record %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
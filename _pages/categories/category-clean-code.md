---
title: "Clean Code"
layout: archive
permalink: categories/clean-code
---

{% assign posts = site.categories.clean_code %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
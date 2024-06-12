---
title: "News"
layout: archive
permalink: /News
author_profile: true
sidebar:
    nav: "sidebar-category"
---


{% assign posts = site.categories.News %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}

---
title: "Test"
layout: archive
permalink: categories/test
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories['ST'] %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
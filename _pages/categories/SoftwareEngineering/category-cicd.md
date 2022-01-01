---
title: "Ci/CD"
layout: archive
permalink: categories/CICD
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories['CI/CD'] %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
---
layout: projects
icon: fas fa-box
order: 1
title: "Projects"
permalink: /projects/
---

## Projects

<ul>
  {% assign all_projects = site.posts | sort: 'date' %}
  {% for project in all_projects %}
    <li>
      <h2><a href="{{ project.url | relative_url }}">{{ project.title }}</a></h2>
      <p>{{ project.excerpt }}</p>
    </li>
  {% endfor %}
</ul>
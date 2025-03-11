---
layout: tags
icon: fas fa-tags
order: 2
---

## All Projects

<ul>
  {% assign all_projects = site.projects | sort: 'date' %}
  {% for project in all_projects %}
    <li>
      <h2><a href="{{ project.url | relative_url }}">{{ project.title }}</a></h2>
      <p>{{ project.excerpt }}</p>
    </li>
  {% endfor %}
</ul>
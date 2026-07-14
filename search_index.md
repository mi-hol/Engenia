---
layout: null
---
{
  "debug": {
    "static_files_count": {{ site.static_files | size }},
    "pages_count": {{ site.pages | size }},
    "posts_count": {{ site.posts | size }}
  },
  "sample_static_files": [
    {% for file in site.static_files limit: 5 %}
    "{{ file.path }}"{% unless forloop.last %},{% endunless %}
    {% endfor %}
  ],
  "sample_pages": [
    {% for page in site.pages limit: 5 %}
    "{{ page.url }}"{% unless forloop.last %},{% endunless %}
    {% endfor %}
  ]
}

---
layout: null
---
[
{% for page in site.data.pages %}
  {
    "title": {{ page.title | jsonify }},
    "date": "N/A",
    "tags": [],
    "keywords": {
      {% assign sanitized = page.title | downcase %}
      {% assign words = sanitized | split: ' ' %}
      {% for word in words %}
        {% if word.size > 1 %}
          {{ word | jsonify }}: 1{% unless forloop.last %},{% endunless %}
        {% endif %}
      {% endfor %}
    },
    "url": "{{ site.baseurl }}{{ page.path }}",
    "excerpt": ""
  }{% unless forloop.last %},{% endunless %}
{% endfor %}
]

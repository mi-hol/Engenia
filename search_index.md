---
layout: null
---
{%- assign stop_words = site.data.stop_words.words -%}
{%- assign split_chars = site.data.split_chars.chars -%}
[
{%- for page in site.pages -%}
  {%- unless page.url == '/Engenia/search/' or page.url == '/Engenia/' or page.url contains 'search_index' -%}
  {%- assign page_date = page.date | default: page.last_modified_at | default: 'N/A' -%}
  {%- if page_date != 'N/A' -%}
    {%- assign formatted_date = page_date | date: '%b %-d, %Y' -%}
  {%- else -%}
    {%- assign formatted_date = 'N/A' -%}
  {%- endif -%}
  {"title":{{ page.title | default: 'Untitled' | jsonify }},"date":"{{ formatted_date }}","tags":[{%- for tag in page.tags -%}{{ tag | jsonify }}{% unless forloop.last %},{% endunless %}{%- endfor -%}],"keywords":{
    {%- assign sanitized_content = page.content | append: ' ' | append: page.title | append: page.tags | join: " " | strip_html | downcase | escape -%}
    {%- for char in split_chars -%}
      {%- assign sanitized_content = sanitized_content | replace: char, ' ' -%}
    {%- endfor -%}
    {%- assign words = sanitized_content | split: ' ' -%}
    {%- assign filtered_words = words | where_exp: "word", "word.size > 1" -%}
    {%- assign stop_words_filtered_words = '' -%}
    {%- for word in filtered_words -%}
      {%- unless stop_words contains word -%}
        {%- if stop_words_filtered_words == '' -%}
          {%- assign stop_words_filtered_words = word -%}
        {%- else -%}
          {%- assign stop_words_filtered_words = stop_words_filtered_words | append: ',' | append: word -%}
        {%- endif -%}
      {%- endunless -%}
    {%- endfor -%}
    {%- assign filtered_words = stop_words_filtered_words | split: ',' -%}
    {%- assign grouped_words = filtered_words | group_by_exp: "word", "word" -%}
    {%- for group in grouped_words -%}
      {{ group.name | jsonify }}:{{ group.items | size }}{% unless forloop.last %},{% endunless %}
    {%- endfor -%}
  },"url":"{{ page.url | relative_url }}","excerpt":{{ page.excerpt | default: '' | strip_html | truncate: 200 | strip_newlines | jsonify }}}{% unless forloop.last %},{% endunless %}
  {%- endunless -%}
{%- endfor -%}
]

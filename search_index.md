---
layout: null
---
{%- assign stop_words = site.data.stop_words.words -%}
{%- assign split_chars = site.data.split_chars.chars -%}
{
  "debug": {
    "static_files_count": {{ site.static_files | size }},
    "pages_count": {{ site.pages | size }},
    "posts_count": {{ site.posts | size }},
    "static_files_sample": [
      {%- for file in site.static_files limit: 10 -%}
        "{{ file.path }}"{% unless forloop.last %},{% endunless %}
      {%- endfor -%}
    ]
  },
  "index": [
{%- for file in site.static_files -%}
  {%- if file.path contains '.html' and file.path != '/search.json' and file.path != '/search_index.json' -%}
    {%- assign filename = file.name | remove: '.html' -%}
    {%- assign title = filename | replace: '-', ' ' | replace: '_', ' ' -%}
    {%- assign sanitized_title = title | downcase -%}
    {%- assign words = sanitized_title | split: ' ' -%}
    {%- assign stop_words_filtered_words = '' -%}
    {%- for word in words -%}
      {%- if word.size > 1 -%}
        {%- unless stop_words contains word -%}
          {%- if stop_words_filtered_words == '' -%}
            {%- assign stop_words_filtered_words = word -%}
          {%- else -%}
            {%- assign stop_words_filtered_words = stop_words_filtered_words | append: ',' | append: word -%}
          {%- endif -%}
        {%- endunless -%}
      {%- endif -%}
    {%- endfor -%}
    {%- assign filtered_words = stop_words_filtered_words | split: ',' -%}
    {%- assign grouped_words = filtered_words | group_by_exp: "word", "word" -%}
    {
      "title":{{ title | jsonify }},
      "date":"N/A",
      "tags":[],
      "keywords":{
        {%- for group in grouped_words -%}
          {{ group.name | jsonify }}:{{ group.items | size }}{% unless forloop.last %},{% endunless %}
        {%- endfor -%}
      },
      "url":"{{ site.baseurl }}{{ file.path | replace: './', '/' }}",
      "excerpt":""
    }{% unless forloop.last %},{% endunless %}
  {%- endif -%}
{%- endfor -%}
{%- for page in site.pages -%}
  {%- unless page.url == '/Engenia/search/' or page.url == '/Engenia/' -%}
    ,
    {%- assign page_content = page.content | append: ' ' | append: page.title | strip_html | downcase | escape -%}
    {%- for char in split_chars -%}
      {%- assign page_content = page_content | replace: char, ' ' -%}
    {%- endfor -%}
    {%- assign page_words = page_content | split: ' ' -%}
    {%- assign page_filtered_words = page_words | where_exp: "word", "word.size > 1" -%}
    {%- assign page_stop_words_filtered = '' -%}
    {%- for word in page_filtered_words -%}
      {%- unless stop_words contains word -%}
        {%- if page_stop_words_filtered == '' -%}
          {%- assign page_stop_words_filtered = word -%}
        {%- else -%}
          {%- assign page_stop_words_filtered = page_stop_words_filtered | append: ',' | append: word -%}
        {%- endif -%}
      {%- endunless -%}
    {%- endfor -%}
    {%- assign page_filtered_words = page_stop_words_filtered | split: ',' -%}
    {%- assign page_grouped_words = page_filtered_words | group_by_exp: "word", "word" -%}
    {
      "title":{{ page.title | default: 'Untitled' | jsonify }},
      "date":"N/A",
      "tags":[],
      "keywords":{
        {%- for group in page_grouped_words -%}
          {{ group.name | jsonify }}:{{ group.items | size }}{% unless forloop.last %},{% endunless %}
        {%- endfor -%}
      },
      "url":"{{ page.url | relative_url }}",
      "excerpt":{{ page.excerpt | default: '' | strip_html | truncate: 200 | strip_newlines | jsonify }}
    }
  {%- endunless -%}
{%- endfor -%}
  ]
}

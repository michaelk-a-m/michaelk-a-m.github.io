---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults
#         <div class="blog-snippet">
#     <h4><a href="{{ root_url }}{{ post.url }}">{{ post.title }}</a></h4>
#     <h6>{{ post.date | date_to_string}}</h6>
#     <p>{{ post.description }}</p>
# </div>
layout: default
title: Home
---

<div class="blog-index">  
  {% for post in site.posts %}
    {% if post.title != null and post.published != false %}
        {% assign content = post.description %}
        {% include post_preview.html %}
    {% endif %}
  {% endfor %}
</div>
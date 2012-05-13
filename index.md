---
layout: page
title: Latest Post
---
<article>
  <time datetime="{{ site.posts.first.date | xmlschema }}">{{ site.posts.first.date | date: "%d %b" }}</time>
  <h2><a href="{{ site.posts.first.url }}">{{ site.posts.first.title }}</a></h2>
  {{ site.posts.first.content }}
</article>
<hr>


<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>




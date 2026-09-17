---
layout: page
title: "BIBLE"
permalink: /bible/
main_nav: false
lang: ko
---
{% assign bible_posts = site.categories.Bible %}
{% if bible_posts and bible_posts.size > 0 %}
<ul class="posts-list category-posts">
{% for post in bible_posts %}
  <li>
    <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
    <time class="post-date" datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y. %m. %d" }}</time>
  </li>
{% endfor %}
</ul>
{% else %}
<div class="empty-state">
  <span class="empty-state-mark" aria-hidden="true">✦</span>
  <p>아직 등록된 성경 글이 없습니다.</p>
  <small>새 글은 이곳에 날짜순으로 표시됩니다.</small>
</div>
{% endif %}

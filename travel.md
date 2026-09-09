---
layout: page
title: "Field Notes"
permalink: /travel/
main_nav: false
---
<p class="section-intro">Places, detours, and observations gathered along the way.</p>

{% assign travel_posts = site.categories.Travel %}
{% if travel_posts and travel_posts.size > 0 %}
<ul class="posts-list category-posts">
{% for post in travel_posts %}
  <li>
    <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
    <time class="post-date" datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y. %m. %d" }}</time>
  </li>
{% endfor %}
</ul>
{% else %}
<div class="empty-state">
  <span class="empty-state-mark" aria-hidden="true">—</span>
  <p>No field notes yet.</p>
  <small>The next journey will leave something here.</small>
</div>
{% endif %}

---
layout: page
title: "Engineering Notes"
permalink: /development/
main_nav: false
---
<p class="section-intro">Software, automotive security, artificial intelligence, and the details worth remembering.</p>

{% assign sorted_posts = site.posts | sort: "date" | reverse %}
<ul class="posts-list category-posts">
{% for post in sorted_posts %}
  <li>
    <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
    <time class="post-date" datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y. %m. %d" }}</time>
  </li>
{% endfor %}
</ul>

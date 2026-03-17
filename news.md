---
layout: page
title: News & Updates
permalink: /news/
---

<div class="prose-block">
  <p>Use the <code>_news</code> collection for grants, publications, events, hiring notices, and short project updates.</p>
</div>

<div class="news-list">
  {% for post in site.news %}
  <article class="news-item">
    <time class="news-date" datetime="{{ post.date | date_to_xmlschema }}">
      {{ post.date | date: "%B %-d, %Y" }}
    </time>
    <div class="news-content">
      <h3 class="news-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
      <p class="news-excerpt">{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
      <a href="{{ post.url | relative_url }}" class="read-more">Read more -></a>
    </div>
  </article>
  {% endfor %}
</div>

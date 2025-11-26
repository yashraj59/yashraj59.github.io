---
layout: default
title: Blog Archive
permalink: /archive/
---

<section class="page-lede">
  <p class="eyebrow">Archive</p>
  <h1>Every post, neatly stacked</h1>
  <p class="lede-copy">Browse everything I've published so far. It's a clean timeline so you can trace ideas as they evolved.</p>
</section>

<section class="archive-list">
  {%- if site.posts.size == 0 -%}
  <p class="muted">Nothing here just yet. Check back soon.</p>
  {%- else -%}
  {%- assign posts_by_year = site.posts | group_by_exp: 'post', 'post.date | date: "%Y"' -%}
  {%- for year in posts_by_year -%}
  <div class="archive-year-group">
    <h2>{{ year.name }}</h2>
    <div class="archive-items">
      {%- for post in year.items -%}
      <a class="archive-item" href="{{ post.url | relative_url }}">
        <span class="archive-date">{{ post.date | date: "%b %d" }}</span>
        <span class="archive-title">{{ post.title }}</span>
      </a>
      {%- endfor -%}
    </div>
  </div>
  {%- endfor -%}
  {%- endif -%}
</section>

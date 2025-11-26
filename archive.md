---
layout: default
title: Archive
permalink: /archive/
---

<section class="intro intro--tight">
  <p class="eyebrow">Archive</p>
  <h1 class="intro__title">Every post, in order.</h1>
  <p class="intro__lede">Use this list like a changelog of my thinking. Each year acts as a waypoint so you can jump to the era you're curious about.</p>
</section>

<section class="timeline">
  {%- if site.posts.size == 0 -%}
  <p class="muted">Nothing to see yet.</p>
  {%- else -%}
  {%- assign posts_by_year = site.posts | group_by_exp: 'post', 'post.date | date: "%Y"' -%}
  {%- for year in posts_by_year -%}
  <div class="timeline__year">
    <div class="timeline__marker">{{ year.name }}</div>
    <div class="timeline__items">
      {%- for post in year.items -%}
      <a class="timeline__item" href="{{ post.url | relative_url }}">
        <span class="timeline__date">{{ post.date | date: "%b %d" }}</span>
        <span class="timeline__title">{{ post.title }}</span>
      </a>
      {%- endfor -%}
    </div>
  </div>
  {%- endfor -%}
  {%- endif -%}
</section>

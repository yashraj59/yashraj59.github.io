---
layout: default
title: Home
---

<section class="hero">
  <p class="eyebrow">Hi, I'm Yash Raj</p>
  <h1 class="hero-title">Quiet notes on AI, art, and building useful products.</h1>
  <p class="hero-copy">I enjoy documenting experiments, sharing lessons from the tools I build, and keeping the writing calm and intentional. Grab a drink, explore the posts, and reach out if something resonates.</p>
  <div class="hero-actions">
    <a class="btn primary" href="#recent">Browse the writing</a>
    <a class="btn ghost" href="mailto:{{ site.email }}">Say hello</a>
  </div>
</section>

<section class="post-feed" id="recent">
  <div class="section-heading">
    <h2>Latest writing</h2>
    <a class="text-link" href="{{ '/archive/' | relative_url }}">View archive →</a>
  </div>
  {%- if site.posts.size > 0 -%}
  <div class="post-grid">
    {%- for post in site.posts -%}
    <article class="post-card">
      <p class="post-card-date">{{ post.date | date: "%b %d, %Y" }}</p>
      <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
      <p class="post-card-excerpt">{{ post.excerpt | strip_html | strip_newlines | truncate: 150 }}</p>
      <a class="post-card-link" href="{{ post.url | relative_url }}">Read essay</a>
    </article>
    {%- endfor -%}
  </div>
  {%- else -%}
  <p class="muted">No posts yet, but something thoughtful is on the way.</p>
  {%- endif -%}
</section>

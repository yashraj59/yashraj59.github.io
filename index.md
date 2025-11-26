---
layout: default
title: Home
---

<section class="intro">
  <p class="eyebrow">Field notes</p>
  <h1 class="intro__title">Calm observations on AI, creative work, and the tools I ship.</h1>
  <p class="intro__lede">I am Yash Raj. I prefer quiet interfaces, practical experiments, and essays that can be read in one sitting. This site is where I document the work so it stays useful for others.</p>
  <div class="intro__actions">
    <a class="pill" href="mailto:{{ site.email }}">Email</a>
    <a class="pill" href="https://github.com/{{ site.github_username }}" target="_blank" rel="noopener">GitHub</a>
    <a class="pill" href="{{ '/archive/' | relative_url }}">Full archive</a>
  </div>
</section>

<section class="stack" id="recent">
  <header class="stack__header">
    <h2>Recent writing</h2>
    <a class="plain-link" href="{{ '/archive/' | relative_url }}">Browse everything →</a>
  </header>

  {%- if site.posts.size > 0 -%}
  <ol class="post-stack" reversed>
    {%- for post in site.posts -%}
    <li class="post-card">
      <div class="post-card__meta">
        <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%d %b %Y" }}</time>
        {%- if post.categories and post.categories.size > 0 -%}
        <span class="post-card__tags">{{ post.categories | join: ', ' }}</span>
        {%- endif -%}
      </div>
      <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
      <p>{{ post.excerpt | strip_html | strip_newlines | truncate: 140 }}</p>
    </li>
    {%- endfor -%}
  </ol>
  {%- else -%}
  <p class="muted">No posts yet.</p>
  {%- endif -%}
</section>

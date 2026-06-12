---
layout: default
title: Home
---

<section class="intro">
  <p class="eyebrow">Field notes</p>
  <h1 class="intro__title">Calm observations on AI, creative work, and the tools I ship.</h1>
  <p class="intro__lede">I am Yash Raj. I prefer quiet interfaces, practical experiments, and essays that can be read in one sitting. This site is where I document the work so it stays useful for others.</p>
</section>

<section class="stack" id="recent">
  <header class="stack__header">
    <h2>Recent writing</h2>
    <a class="plain-link" href="{{ '/archive/' | relative_url }}">Browse everything →</a>
  </header>

  {%- if site.posts.size > 0 -%}
  {%- assign total = site.posts | size -%}
  <ol class="issue-list" reversed>
    {%- for post in site.posts -%}
    {%- assign n = total | minus: forloop.index0 -%}
    {%- if n < 10 -%}{%- assign n_padded = n | prepend: '0' -%}{%- else -%}{%- assign n_padded = n -%}{%- endif -%}
    <li class="issue-row">
      <a href="{{ post.url | relative_url }}">
        <span class="issue-row__rail">
          <span class="issue-no">No.&nbsp;{{ n_padded }}</span>
          <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%b %-d, %Y" }}</time>
        </span>
        <span class="issue-row__main">
          <h3 class="issue-title">{{ post.title }}</h3>
          {%- assign dek = post.description | default: post.excerpt | strip_html | strip_newlines | strip -%}
          {%- if dek != '' -%}<p class="issue-dek">{{ dek | truncate: 120 }}</p>{%- endif -%}
        </span>
      </a>
    </li>
    {%- endfor -%}
  </ol>
  {%- else -%}
  <p class="muted">No posts yet.</p>
  {%- endif -%}
</section>

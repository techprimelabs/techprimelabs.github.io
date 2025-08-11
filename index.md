---
layout: default
title: Welcome
---

<section class="hero" id="home" tabindex="0" aria-label="Welcome section">
  <h1>Welcome to TechPrime</h1>
  <p><em>Articles and blogs for exploring technology, code, and innovation.</em></p>
  <button class="btn-primary" onclick="document.getElementById('blog').scrollIntoView({behavior:'smooth'})" aria-label="Scroll to Blog section">
    Read the Blog
    <svg viewBox="0 0 24 24" aria-hidden="true" focusable="false" style="width:16px; height:16px; fill:none; stroke-width:2; stroke-linecap:round; stroke-linejoin:round; stroke:white; vertical-align:middle; margin-left:6px;">
      <path d="M7 10l5 5 5-5" />
    </svg>
  </button>
</section>

<section class="featured-blogs" id="blog" aria-label="Featured blog posts">
  <h2>Featured Blog Posts</h2>
  <div class="blog-grid">
    {% for post in site.posts %}
    <article class="blog-card" tabindex="0" role="article" aria-labelledby="post-{{ forloop.index }}-title" aria-describedby="post-{{ forloop.index }}-desc">
      <h3 id="post-{{ forloop.index }}-title">
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </h3>
      <p id="post-{{ forloop.index }}-desc">{{ post.excerpt | strip_html | truncate: 140 }}</p>
      <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time>
    </article>
    {% endfor %}
  </div>
</section>

<section class="about" id="about" aria-label="About TechPrime">
  <h2>About TechPrime</h2>
  <p>TechPrime is a leading technology company dedicated to delivering innovative software solutions. This site features our latest blogs and articles, where we explore new ideas and cutting-edge technologies.</p>
</section>

<section class="contact" id="contact" aria-label="Contact TechPrime">
  <h2>Contact Us</h2>
  <p>Have questions or want to collaborate? Reach out to us at <a href="mailto:{{ site.email }}">{{ site.email }}</a></p>
</section>

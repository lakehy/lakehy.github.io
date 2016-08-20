---
layout: default
---

# Everything is a stream

## Posts
<ul class="post-list">
  {% for post in site.posts %}
    <li>
      <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
      <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

## ToDo

- Write the gitbook about build environments

subscribe [via Rss](feed.xml)

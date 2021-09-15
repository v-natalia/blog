---
layout: post
title:  "The Challenge"
date:   2019-04-02 10:20:00 +0200
moment: 2nd April, 2019
categories: javascript-30
permalink: /posts/js-30-challenge
bg: workcreate.jpg
---

Let's do this challenge.

I will post here all the results of the challenge.
All in the same place, because nobody wants 30 nerdy posts, and also, because, doing this I can prove that I know how to iterate when using jekyll. Lol.

{% for post in site.days %}
  <ul>
    <li style="list-style-type: none">
      <h3><a href="{{ post.url | relative_url }}">{{ post.name }}</a></h3><p>{{ post.description }}</p>
    </li>
  </ul>
{% endfor %}

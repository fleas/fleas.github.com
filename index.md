---
layout: page
title: I'll get to it, I really will..
tagline: Clever Taglinolium
---
{% include JB/setup %}


## Fleas Posts (They're coming!)

I may even show some bash one-liners

    $ echo "for realz!"

"My posts list".

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## To-Do

There is nothing here right now, [NOTHING!](http://www.nothing.com)!
When I have time, I will.. 


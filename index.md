---
layout: default
---

## Update

I've removed most content on the blog, and instead pushed an old branch from a
new repo to wipe history too. This was in response to some comments that made
me consider that an anonymous blog might be better for some of the content.

This is only going to be technical content from now.

## Index

<ul>
  {% for post in site.posts %}
    {% if page.minutes %}
      {% assign minutes = page.minutes %}
    {% else %}
      {% assign minutes = post.content | number_of_words | divided_by: 180 %}
      {% if minutes == 0 %}{% assign minutes = 1 %}{% endif %}
    {% endif %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
        <small> {{ minutes }} minute read ({{ post.content | number_of_words }} words) </small>

      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>


## Old Intro

Let's start with three deep breaths.

Several years ago, I had an anonymous blog.

It was pretentious, flashy, and (from what anonymous people told me) moderately
interesting.

I went through a phase where my writing was mostly personal, and I deleted all
my public writing. These days, I've been interested in writing more from a
technical standpoint, and in exploring perspectives and narratives.

This is the reborn old blog, pretentious name and all.

I think it's time I got used to putting things online again, and this is an
attempt to increase my publishing output. Expect this to be raw, hastily
written, not carefully edited, extremely honest and a stream of consciousness
directly from my keyboard. I'll try to not take down things that I have second
thoughts about, but no promises.

Posts of a technical nature will stay up in some form or another for linking.
Any posts not tagged permanent should not be expected to be up.

The font, the structure and the theme are a work in progress. The plan is to
start simple, and slowly lure my largely reticent muse out into the productive
and welcoming open world.

The occassional emoji is a reference, and can be safely ignored.

Thank you, and I hope to entertain, teach and learn.

Karthikeya

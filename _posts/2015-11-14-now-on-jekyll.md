---
layout: post
title: "On Jekyll"
date: 2015-11-14 14:18:00 +0000
tags: jekyll ruby webapps meta
comments: false
---

I've moved this here blog thing from Joomla to Jekyll.

I've not (yet) moved the site content over to this new site,
as currently I'm unsure if any of the content I had written was really worth
preserving, or if it was best to start anew.

The resons for moving to Jekyll are simple and mostly obvious:

## No requirement for a server-side scripting language or database

This should be pretty clear but the main reason is that
working with PHP and the popular PHP CMSes out there on a daily basis
teaches you to be wary of them, this deployment mechanism means
that the site itself cannot be hacked directly or used as an attack vector.

Of course, nothing is truely "hack-proof" so precautions still need to be taken,
but it removes the vulnerabilities that a CMS like Wordpress would introduce.


## Native Markdown Editing

Because most CMSes are not designed for people like me, who use markdown as their
de-facto standard for formatting prose text. Many use an HTML WYSIWYG editor,
which is great for most users, but ends up making editing less efficient,
and the output less elegant. It also means that the only format the content can
be delivered in is HTML.

## No dedicated editing application

Using Jekyll, and a git based deployment process means that deploying changes
to the site is simple and easy, and I can do it anywhere thanks to github's
online editor. I only need to be logged into my github account in order to
make changes or write a new post.

Currently, I'm using a git hook to rebuild the site and publish the changes,
this is triggered by a git push to my server.

This script clones the repo from github to a temporary directory,
builds it to the public directory, then deletes the temporary copy.


## Warp Speed

Finally, this item actually returns to my first point, but the lack of server-side
programming in this site means that it can be delivered at break-neck speeds.
Even without any kind of CDN or HTTP accellerator like Varnish, a well tuned nginx configuration
and a lack of server-side scripting means that the all-important TTFB is much lower.


I hope that these items above and the transition to Jekyll will give me
cause to write better, more frequent posts here.

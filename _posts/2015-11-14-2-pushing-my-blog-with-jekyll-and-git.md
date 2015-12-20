---
layout: post
title: "Pushing Posts with Jekyll & Git"
date: 2015-11-14 18:30:00 +0000
tags: jekyll ruby webapps meta git
comments: true
---

So I've made some changes here today and the bulk of that is switching over from
Joomla to Jekyll.

With this change of systems comes a change in how things are deployed.
Jekyll is a static site generator. It builds a site of entirely static files
from dynamic sources.

There are a couple of ways to make this process easier when running a site built
using Jekyll, most notably Github pages.

However, I wanted something a bit different, and something which I could run
against my own SSL certificates. Given these requirements I've chosen to
continue to run the site on my own nginx-based web server.

## Deployment

Pushing an update to the site is a pretty simple process. The entire work-flow
is controlled by git.

Once I make some changes, such as this post. I commit the changes to git.
I push these changes to github.

I then also trigger a push of the site independently using a git remote set up
on the server:

~~~~bash
git push site	
~~~~

This does a push of the changes to my server, which has a hook set up for the
`post-receive` event.
In this script, I perform the following actions:

1. Clone the site source from github to a temporary folder.
2. Build the site within that temporary folder using `jekyll build`
3. If the build succeeds:
	3.1 Replace the existing site with the new one
	3.2 Delete the temporary folder
4. If the build fails the temporary folder is kept so I can investigate the cause.

All this is handled by a simple shell script of about 20 lines:


~~~~bash
echo "jekyll_build: cloning $GIT_REPO to $TMP_CLONE"
git clone $GIT_REPO $TMP_CLONE

echo "jekyll_build: building site from $TMP_CLONE to $PUBLIC_WWW"
"$JEKYLL_BIN" build -s "$TMP_CLONE" -d "$PUBLIC_WWW"

echo "jekyll_build: cleaning up - deleting $TMP_CLONE"
rm -rf $TMP_CLONE

echo "jekyll_build: DONE!"
exit
~~~~

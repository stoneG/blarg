Title: Trying out the static blog thing
Date: 2013-01-08 16:35
Author: Sitong Peng
Tags: static blog, pelican, python, the architect, jinja2
Slug: trying-out-the-static-blog-thing
Summary: Setting up a static blog site using Pelican

Since I started actively programming a few months ago, I've learned much about what technology I take for granted. In order to help me appreciate the many abstractions that our lives are built upon, I've made it my goal to be more technologically self-sufficent. The first step was to revamp my [personal website](http://www.sitongpeng.com). Next up was to move my code blog from Tumblr to my own domain, and then rebuild it on top of a static site/blog generator. That way, I would have complete ownership and control over my own posts.

Static what nots?
-----------------
Static site generators do what they advertise. They take some content you provide, then generate a pre-determined set of static html pages that are web ready. The idea here is that once you finish setting up the styling and format of your site, you can simply focus on ***writing content***. Of course, they have boiler plate templates ready for you too, if you'd rather jump right into writing.

Getting set up
--------------
You should certainly consult the [Pelican docs](https://pelican.readthedocs.org) for the full guide, but here's my general guide on how to get started.

Pelican is written in Python (major selling point for me), so install it and it's dependencies using virtualenv and pip. Pelican only supports [reST](http://docutils.sourceforge.net/rst.html) out of the box but provides [Markdown](http://daringfireball.net/projects/markdown/) support upon installation of the [Python-Markdown](http://packages.python.org/Markdown/) library. I use Markdown for my posts.

Then, you'll want to make a `settings.py` file somewhere in your project folder. This file will hold the global variables you set for Pelican. The important ones include:
```
AUTHOR = 'YOUR NAME'
SITENAME = 'YOUR SITE NAME'
SITEURL = 'http://your-site.com'
```
For the purposes of setting up a blog on Pelican, you will want to set `DEFAULT_PAGINATION` to be the (number of posts)/page and `DEFAULT_ORPHANS` as the largest remainder of posts you'll allow on your last page. For my setup, I selected 5 posts/page, and no more than 3 posts on the last page (so up to 8 posts on the last page).

You can browse over all my selected settings [here](https://github.com/stoneG/blarg/blob/master/settings.py).

Making it yours
---------------
Now, you can certainly use the [default theme](https://github.com/getpelican/pelican-themes/blob/master/notmyidea-cms-fr/screenshot.png) provided by Pelican, or one of their other [sample themes](https://github.com/getpelican/pelican-themes/blob/master/waterspill/screenshot.png), but I prefer to make a theme that suits my own tastes. Reading the Pelican documentation and working through the quickstart guide took up half of my weekend, which means I spent the other half on designing my theme.

You'll need some familiarity with [Jinja2](http://jinja.pocoo.org/docs/) templates. At the minimum, you should read about the conditionals/loop syntax (especially loop attributes) and template inheritance (include and block). I believe Jinja2's templating system is similar to Django's, so you should feel at home if that's your forte.

Pelican is specific about what file structure it wants in a custom theme. The [documentation](http://docs.getpelican.com/en/3.1.1/themes.html) outlines the structure, but doesn't give you adequate information on what attributes you can access from variable objects. If you need specific functionality beyond what objects and object attributes others have done in the sample themes, you'll probably just need to read through the Pelican source or ask some questions in their IRC channel.

I ended up starting with the default theme (called: notmyidea) templates and stripping it of everything I didn't want to use. For instance, I had no need for the category and author functionalities. I don't want to categorize posts and I'll be the only author on this blog, therefore, I did not supply:
```
categories.html
category.html
author.html
authors.html
```
Pelican is smart enough to just use it's base templates from the Simple theme if you don't provide all the desired html templates.

You will, however, want to make the basic html frame for your blog and save it as `base.html`, as all other html templates will inherit from it. Otherwise, use the default theme's templates as a guide for building your custom templates.

Once you have your html templates set, you'll want to work on the CSS, which you will save in `static/css` folder.

The theme for this blog is [here](https://github.com/stoneG/blarg/tree/master/themes/sitong). Please give me a shoutout in the footer if you want to use it ;).

Workflow
--------
So once you have your theme, you're ready to create content! I host my pages on [Github Pages](http://pages.github.com/), but you choose your own host.

Let's go through how I am publishing this post. First I write it (in vim!) and save it in my content folder, which stores all my posts for this year.
```
content/2013/trying-out-the-static-blog-thing.md
```
I like to move out to the root of my Blog folder, then run Pelican to update all my static pages.
```
$ pelican content/2013/ -s path/to/settings.py
```
The new static pages will be placed in
```
output/
```
but you can always change that path in your `settings.py`.

Then I simply `$ git add .` in my output folder, throw on a commit message, then push it to Github.

Final thoughts
--------------
The entire set up process takes a bit of time, especially if you're making your own theme, but it's always nice to have ownership over your own content. That's just something you don't get on Tumblr or other browser based blogs. Pelican is an awesome option for anyone looking for a static blog generator and wanting to work in Python, I heartily recommend it.

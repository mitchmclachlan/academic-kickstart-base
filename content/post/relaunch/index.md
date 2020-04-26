+++
title = "Site Relaunch"
subtitle = "I finally ditched WordPress"

date = 2018-01-13T00:00:00
lastmod = 2018-01-13T00:00:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Mitch McLachlan"]

tags = ["Web", "Brand"]
summary = "I finally ditched WordPress and I'm playing with Hugo for static sites."

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects = ["Web"]

# Featured image
# To use, add an image named `featured.jpg/png` to your project's folder.
[image]
  # Caption (optional)
  caption = "Image credit: [**Hugo**](https://gohugo.io/)"

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""

  # Show image only in page previews?
  preview_only = false

# Set captions for image gallery.

+++

It's been over 5 years since I've worked on my site, so it was time for reload. The last iteration of the site was built on WordPress, including a theme I built from scratch (you'll find a theme here.) It was a lot of work to build the theme, but WP is pretty simple to author content in. I just didn't have a lot to say.

I chose [Hugo](https://gohugo.io/) as my new platform. It was a rather hasty decision. I had never  heard of Hugo until a couple weeks ago when I saw an article come through [R-bloggers](https://www.r-bloggers.com/) about [rOpenSci using Hugo](https://ropensci.org/technotes/2019/01/09/hugo/). I've really gotten into data science over the past 12 months and the R-bloggers daily newsletter has made me want to start writing again. I was looking for a _lightning fast_, easy-to-build and maintain platform that also made authoring as fast as possible. Here are some reason's Hugo immediately resonated with me:

* **The content is static** and served as HTML immediately at request. WordPress is powered by PHP and MySQL, and has to do a bunch of server-side processing and fetching from databases for every page load. While there are plugins for caching and speed for WP, lightning fast speed is the core feature of static site generators (SSGs) like Hugo. (More on speed later...)
* **Authoring in Markdown.** I've used markdown for a few years, and hate WYSIWYG editors, so do most of content authoring in raw HTML. It will take a little time to get used to the formatting for site content instead of marking up things Jupyter Notebooks, Jira tickets or Basecamp posts (though BC dropped MD support some time ago).

I'm already seeing the benefits:

1. I've written something for the first time in 5 years
1. The new site is drastically faster than the old.

Here's a before and after stack breakdown:

|Component|Old Site|New Site|
|---|---|---|
|Platform|[Self-hosted WordPress](https://wordpress.org/)|[Hugo](https://gohugo.io/)|
|Hosting|[Dreamhost Shared Hosting](https://www.dreamhost.com/hosting/shared/)|[Netlify](https://www.netlify.com/)|
|Theme|Built from scratch inspired by [Nathan Barry's personal site](https://nathanbarry.com/)|[Academic for Hugo](https://themes.gohugo.io/academic/) by [George Cushen](https://georgecushen.com/)|
|Google Page Speed: Mobile|82|98|
|Google Page Speed: Desktop|91|100|

I'm not in academia, but I liked this theme as an easy way to cover the following needs:

1. Résumé/CV
1. Portfolio
1. Blog

I'll come back later and write about the work I've been doing to making the site even faster than the stock theme + Hugo combo.

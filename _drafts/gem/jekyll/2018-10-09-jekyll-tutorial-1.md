---
layout: post
author: Wouter Van Schandevijl
title:  "Static site generation with Jekyll: A tutorial"
date:   2018-10-09 12:00:00 +0200
categories: ruby
tags: [tutorial,jekyll]
desc: >
  
img: Jekyll.png
interesting:
  - desc: 
    url: 
  - desc: 
    url: 
  - desc: 
    url: 
series: jekyll-tut
toc:
    title: Jekyll series: Part 1
    icon: eye-slash
---

https://github.com/Shopify/liquid/wiki/Liquid-for-Designers
https://learn.cloudcannon.com/jekyll-cheat-sheet/
https://bl.ocks.org/roachhd/2909c695979764498922
https://jekyllrb.com/docs/templates/#filters

I picked Jekyll to create this site. Not just because of the name, 
but mostly due to GitHub Pages, which turns a simple `git push` into
'put the latest version online'. Which is awesome.

This part of the tutorial is about setting up your very own Jekyll site
and introducing you to the different Jekyll conventions and technologies
which can be a bit daunting at first.

<!--more-->

# Quick setup

```sh
# Install
gem install jekyll bundler

# Create
jekyll new my-awesome-site

# Serve
cd my-awesome-site
bundle exec jekyll serve
# Available at http://localhost:4000
```

Jekyll serve will process all files in folders not starting with an underscore and
release them to the `_site` folder. 

All folders that do start with an underscore are Jekyll specific folders or get
included in some other way, usually in `config.yml`.

Processing is done for files with front matter, all other files are copied straight to `_site`.



# Site configuration

The generated `_config.yml` is a [YAML][YAML-Post] file containing global configuration
that is used in all pages, posts etc. All other files can define page specific
configuration called front matter. The same YAML syntax is used for the front matter.

A `_config.yml` may look like this
```yml
title: Your awesome site title
description: > # > means to ignore newlines until "theme:"
  Write a awesome
  description for
  your kewl site!
theme: minima
```

You can add any variables you'd like and access them in a file containing front matter like `{{ site.title }}`.
Any change to `_config.yml` requires a restart of `bundle exec jekyll serve`.

<!-- See [part 2: Front Matter][part-2] for the YAML syntax.
See [Part 3: _config.yml][part-3] for available options. -->


The generated file `about.md` has the following front matter

<!-- ATTN: Doing the three dashes for front matter here breaks jekyll-paginate -->
```

layout: page
title: About
permalink: /about/

# About page content starts here
Show _config.yml var: {{ site.theme }}
Show front matter var: {{ page.permalink }}
```

The default Markdown renderer is [kramdown](https://kramdown.gettalong.org/).


<!-- See [Part 4: kramdown][part-4] for the md syntax
See [Part 5: Liquid][part-5] for Shopify's Liquid syntax -->


# Themes

By default, the [minima theme][theme-minima] is used. This is defined as `theme: minima` in your `_config.yml`.

For a quick start, you can pick any of the [existing themes][awesome-jekyll-themes]. 
Be sure to check [minimal-mistakes][minimal-mistakes] if you decide to not start from scratch.

To switch themes, update your `./Gemfile ` and `./_config.yml` 
```
# Replace
gem "minima", "~> 2.0"
# With your-theme
gem "jekyll-theme-minimal"
```
And run `bundle update`.

**Some themes:**

{% include github-stars.html url="jekyll/minima" %}
{% include github-stars.html url="mmistakes/minimal-mistakes" %}
{% include github-stars.html url="beautiful-jekyll" %}
{% include github-stars.html url="pages-themes/minimal" %}
{% include github-stars.html url="pages-themes/cayman" %}
{% include github-stars.html url="pages-themes/slate" %}
{% include github-stars.html url="johno/pixyll" %}
{% include github-stars.html url="jeromelachaud/freelancer-theme" %}
{% include github-stars.html url="poole/hyde" %}


Once a theme has been chosen, all roads are still open, as you can override anything and everything.
`bundle show minima` outputs the path to all files of the theme. Copy any file there to your jekyll
directory respecting the relative path and that file will be used instead.

# Layouts

All files in the `_layouts` folder can be used in the `layout` front matter.

`_layouts\default.html`: The base layout used by home.html, page.html and post.html.

## Assets

Any file in `/assets` will be copied to the resulting site. If the file has front matter,
the file will be rendered. --> Already saying above that anything gets copied

assets: https://jekyllrb.com/docs/themes/#assets


- stylesheets: https://jekyllrb.com/docs/themes/#stylesheets




[Official Jekyll Themes docs](https://jekyllrb.com/docs/themes/)

# Conventions and technologies

-- keep this as short as possible --> quickest way to publishing your first post...

Add Where to put normal pages

Put kramdown posts or articles in `_posts`

Collections, Posts and pages
Liquid
plugins

--> deliver rough... the other parts go more in depth
Idea was to do an article about Liquid remember?

--> But first VISUG... :p

## GitHub Pages

Goto the Settings of your project to activate [GitHub Pages](https://pages.github.com/)
for free hosting (also with custom domain name!)

Ruby plugins are not possible here.  
- [The list of usable themes](https://pages.github.com/themes/)
- [The list of usable plugins](https://pages.github.com/versions/)

If you do want other plugins, you can still build your site yourself and push the html
(ie the `_site` folder) to, for example, the `gh-pages` branch.


[YAML-Post]: yaml-tutorial
[part-2]: jekyll-tutorial-2-front-matter
[part-3]: jekyll-tutorial-3-config-yml
[theme-minima]: https://github.com/jekyll/minima
[minimal-mistakes]: https://mmistakes.github.io/minimal-mistakes/
[awesome-jekyll-plugins]: https://github.com/planetjekyll/awesome-jekyll-plugins
[awesome-jekyll-themes]: https://github.com/planetjekyll/awesome-jekyll-themes

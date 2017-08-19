---
layout: post
title:  "Static site generation with Jekyll: A tutorial"
date:   2018-05-23 12:00:00 +0200
categories: ruby
tags: [tutorial,jekyll]
series: jekyll-tut
---

{% include toc title="Jekyll series: Part 1" icon="eye-slash" %}

I picked Jekyll to create this site. Not just because of the name, 
but mostly due to GitHub Pages, which turns a simple `git push` into
'put the latest version online'. Which is awesome.

This part of the tutorial is about setting up your very own Jekyll site
and introducing you to the different Jekyll conventions and technologies
which can be a bit daunting at first.

<!--more-->

## Quick setup

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



## Site configuration

The generated `_config.yml` is a [YAML][yaml-wiki] file containing global configuration
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
Any changes to `_config.yml` require a restart of `bundle exec jekyll serve`.

See [part 2: Front Matter][part-2] for the YAML syntax.
See [Part 3: _config.yml][part-3] for available options.


The generated file `about.md` has the following front matter
```
---
layout: page
title: About
permalink: /about/
---
# About page content starts here
Show _config.yml var: {{ site.theme }}
Show front matter var: {{ page.permalink }}
```

See [Part 4: Kramdown][part-4] for the md syntax
See [Part 5: Liquid][part-5] for Shopify's Liquid syntax


## Themes

By default, the [minima theme][theme-minima] is used. This is defined as `theme: minima` in your `_config.yml`.

For a quick start, you can pick any of the [existing themes][awesome-jekyll-themes]. 
Be sure to check [minimal-mistakes][minimal-mistakes] if you decide to not start from scratch.

To install a theme
```
TODO: actually put this here: gem vs non-gem?
```

Once a theme has been chosen, all roads are still open, as you can override anything and everything.
`bundle show minima` outputs the path to all files of the theme. Copy any file there to your jekyll
directory and that file will be used instead.

All files in the `_layouts` folder can be used in front matter as the 

`_layouts\default.html`: The base layout used by home.html, page.html and post.html.

-----> make a diagram with the minima layout of the layouts/components...

```
_layouts\
	default.html: 
	home.html: 
	page.html: 
	post.html: 
```


## Posts and pages



## Conventions and technologies

### Front Matter

### Kramdown

frontmatter, html
markdown or textile?


### Liquid

### Ruby and gem plugins

### GitHub Pages

- cname
- Plugins issues: push jekyll with allowed plugins or build site locally


[yaml-wiki]: https://en.wikipedia.org/wiki/YAML
[part-2]: jekyll-tutorial-2-front-matter
[part-3]: jekyll-tutorial-3-config-yml
[theme-minima]: https://github.com/jekyll/minima
[minimal-mistakes]: https://mmistakes.github.io/minimal-mistakes/
[awesome-jekyll-plugins]: https://github.com/planetjekyll/awesome-jekyll-plugins
[awesome-jekyll-themes]: https://github.com/planetjekyll/awesome-jekyll-themes
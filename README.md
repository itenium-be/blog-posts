Blog posts
==========
for https://itenium.be

## Kramdown helpers

```markdown
# Notices
{: .notice}, {: .notice--info}, {: .notice--danger}  

# Hide from excerpt:
{: .hide-from-excerpt}

# Table formatting
{: .table-code}, {: .table-margin}, {: .table-excel}

# Styling
{: style="margin-top: 10"}

# Shortcut
{% include kbd k="Win+E" l="" %}

# Github Stars link
{% include github-stars.html url="Laoujin/dotfiles" %}

# <hr>
* * *
```


## New post template

A new post will be published when: 

- The filename starts with a date
- The file is not in the `_drafts` folder
- The front matter date is not in the future
- The submodule of the Jekyll project is updated to include the new post(s)
- The Jekyll project is pushed to Github

**Conventions:**  

- Place image(s) and other resources in the `assets/blog-images` folder of https://github.com/itenium-be/itenium-be.github.io  
- New tag? Assign it an icon in `_date/blog.yml`  
- `lastUpdate` frontmatter needs to be kept in sync manually because sorting on `(updates | last).date` turned out to not be very easy.
- A `autohotkey` code block makes a notice appear about DynaRun


**img**:  

- Image used on the itenium.be front page / blog sidebar
- Size: 360x300

**bigimg**:  

- Image for a post detail page
- Needs to be tested/implemented as soon as we create a blog post witch such a big image...

## Front Matter template

```md
---
layout: post
author: Wouter Van Schandevijl
title:  ""
date:   2017-08-07 00:00:00
desc: >
  This will be displayed for the post on the itenium.be front page
img: javascript-testing-jasmine-customization.png
imgdesc: 
bigimg: ??? # This is actually implemented... Just never used...
categories: javascript # Adding a new category? Update blog.yml to set the icon!
tags: [javascript,tutorial,testing]
series: series-unique-name
url-short: https://bitly.com/ # Retired!
extras:
  - githubproject: https://github.com/itenium-be/
    githubtext: Github link text
  - url: 
    desc: 
  - git: Laoujin/dotfiles
    desc:
interesting:
  - desc: Research
    url: http://www.google.com
toc:
  title: Plugins
  icon: icon-javascript
lastUpdate: 2018-08-22 00:00:00 +0200
updates:
  - date: 2018-08-22 00:00:00 +0200
    desc: Update for Posh-Git v1
---

This will be the excerpt

Not in excerpt
{: .hide-from-excerpt}

<!--more-->

Full article
```

## toc.icon

- Do not prefix with fa to use font-awewome
- Any from [font-mfizz](http://fizzed.com/oss/font-mfizz)
- Site has also loaded a set from [FlatIcon](https://itenium.be/vendor/flat-icon/flaticon.html)

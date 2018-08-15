Blog posts
==========
for https://itenium.be

## Kramdown helpers

Notices: {: .notice}, {: .notice--info}, {: .notice--danger}  

Hide from excerpt: {: .hide-from-excerpt}

Table formatting: {: .table-code}, {: .table-margin}, {: .table-excel}

Styling: {: style="margin-top: 10"}

 {% include kbd k="Win+E" l="" %}


## New post template

A new post will be published when: 

- The filename starts with a date
- The front matter date is not in the future
- The submodule of the Jekyll project is updated to include the new post(s)
- The Jekyll project is pushed to Github

**Front matter img:**  

Place the image(s) in the assets folder of https://github.com/itenium-be/itenium-be.github.io

**img**:  

- Image used on the itenium.be front page / blog sidebar
- Size: 360x300 or 600x497 ?

**bigimg**:  

- Image for a post detail page
- Needs to be tested/implemented as soon as we create a blog post witch such a big image...

**interesting**:  

Provide links to other resources.  
This is not yet implemented in the Jekyll project!


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
bigimg: ???
categories: javascript
tags: [javascript,tutorial,testing]
series: series-unique-name
extras:
    - githubproject: https://github.com/itenium-be/
    - githubtext: Github link text
interesting:
    - http://www.google.com
    - http://www.astalavista.com
toc:
    title: Plugins
    icon: icon-javascript
updated:
    - date: 2017-08-08
      desc: feedback
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


## TODO

There is a front matter `desc` and also the excerpt.
Should those be merged?

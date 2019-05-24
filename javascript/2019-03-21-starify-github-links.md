---
layout: post
author: Wouter Van Schandevijl
title:  "Chrome Extension: Starify Github project links"
date:   2019-03-07 00:00:00
desc: >
  Automatically add Github stars badges to all Github project links.
  Runs automatically on google, stackoverflow and github. Activate with
  Control + Alt + G on any other webpage.
img:
  url: starify-github-links.png
  origin: https://github.com/sindresorhus/awesome-nodejs#mad-science
categories: javascript
tags: [product]
extras:
  - githubproject: https://github.com/itenium-be/github-stars-links
    githubtext: "Chrome Extension source code"
  - url: https://chrome.google.com/webstore/detail/kpficnopciffopkhjpckhkgmnlakcmig
    desc: "Download from the Chrome Web Store"
interesting:
  - url: https://shields.io
    desc: "Shields.io: The Stars badges provider!"
toc:
  title: Starifyication
  icon: icon-javascript
---

A [free and open source Chrome Extension](https://chrome.google.com/webstore/detail/kpficnopciffopkhjpckhkgmnlakcmig)
that adds Github stars badges to all links on a web page.

<br>
**Before**: [https://github.com/sindresorhus/awesome](https://github.com/sindresorhus/awesome)  
**After**: ![sindresorhus/awesome](https://img.shields.io/github/stars/sindresorhus/awesome.svg?style=social&label=Star) [sindresorhus/awesome](https://github.com/sindresorhus/awesome)  


<!--more-->

<br>
Runs automatically on certain pages like Google search results, StackExchange questions,
on Github itself and on Package Manager registries.

{% include kbd k="Control+Alt+G" l="Activate on any other page" %}


# More Examples

## Google Search Results

{% include post/image.html file="starify-github-links-google-results.png" alt="" title="" desc="" %}


## Github Awesome List

{% include post/image.html file="starify-github-links-github-awesome-list.png" alt="" title="" desc="" %}




# Source

Fork the project and modify the source if you want different behavior because... Well there isn't
some sort of settings page :)

Find these variables at the top of [`github-stars.user.js`](https://github.com/itenium-be/github-stars-links/blob/master/github-stars.user.js):

```javascript
// Execute directly on pages matching one of these:
const activateDirectlyOn = [
  'https://stackoverflow.com', 'https://superuser.com', 'https://askubuntu.com',
  'https://serverfault.com', /^https:\/\/.*\.stackexchange\.com/,
  /^https:\/\/(www.)?google\..*\/search/, 'https://www.bing.com',
  'https://github.com', 'https://www.npmjs.com/package', 'https://www.nuget.org/packages',
];

// Otherwise wait for Control + Alt + G:
const isTheHotkey = e => e.ctrlKey && e.altKey && e.code === 'KeyG';

// Shoutout to Shields.io for kindly providing the badges!
const badgeUrl = 'https://img.shields.io/github/stars/{userName}/{repoName}.svg?style=social&label=Star';
```

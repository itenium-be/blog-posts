---
layout: post
author: Wouter Van Schandevijl
title: "Chrome Extension: Starify Links"
subTitle: "Because why stop at GitHub Repositories"
date: 2025-10-23
desc: >
  While I was giving some of the old itenium products some love,
  the starify-github-links update got a bit.. out of hand.
bigimg:
  url: starify-links-big.png
  origin: Midjourney
  prompt: "A majestic galaxy made entirely of golden GitHub stars orbiting around a Chrome logo planet, cinematic poster, wide panoramic view, 'Starify Links' glowing text"
img:
  url: starify-links.png
  origin: Midjourney
  prompt: "Retro 1980s movie poster 'Starify Links' neon typography, chrome reflections, GitHub stars exploding in the background like supernovas"
categories: javascript
tags: [product]
extras:
  - githubproject: https://github.com/itenium-be/github-stars-links
    githubtext: "Chrome Extension source code"
  - url: https://chrome.google.com/webstore/detail/kpficnopciffopkhjpckhkgmnlakcmig
    desc: "Download from the Chrome Web Store"
interesting:
  - url: https://shields.io
    desc: "Shields.io: The badges provider!"
toc:
  title: Starify Links
  icon: icon-git
socials:
  linkedin: ""
  twitter: ""
  facebook: ""
---

Back in 2019 I created a Chrome Extension called
"[Starify Github Links]({% post_url javascript/2019-03-21-starify-github-links %})".

I probably just discovered [sindresorhus awesome](https://github.com/sindresorhus/awesome) lists.
And while awesome lists are, well, awesome, the problem is that while it has so many truly excellent
links, some random guy that created a really niche thing easily ends up in there as well.

So how do you know what to spend your time on, GitHub stars, obviously!

<!--more-->

And that is what this extension did, add star badges to GitHub repository links 😀


# Not my first rodeo

I had already quite some experience with UserScripts / ContentScripts / Greasemonkey Scripts.
Back in the day, I got myself fooled into starting to play Tribal Wars. But... For a game that only
had a web interface, it had a pretty bad one.

So I created a UserScript called the [Sangu Package](https://github.com/SanguPackage/Script)
(for Opera, Firefox and Chrome) that rectified all that; making the UI more user friendly,
more consistent, more powerful.


# One Star

And that is how I ended up with one rating, a one star review for the "Starify Github Links"...
Unlike the current two competitors (which arrived years after my extension, I'd like to add!)
which just add the badge and be done with it, I was integrating it nicely to existing pages.

At some point Google changed their layout and it broke the extension. Someone installed it
and I got that one star review, after which no one bothered installing it anymore.

Maybe rightfully so, as it took me about 2 years before I got around fixing it 😀


# Easy Implementation

If you do the easy implementation, as for example with [Github Star Count](https://chromewebstore.google.com/detail/github-star-count/jahogeehepfohgakggfeeimokcgnmdid), you get this on Google:

![Starify - The Easy Way](/assets/blog-images/starify-links-easy.png)


But that is just unacceptable, so this is what "Starify Links" does, which **will break**
again at some point in the future 😓

![Starify - The Hard Way](/assets/blog-images/starify-links-hard.png)


# Flocking

That extension still had 200 installs, so much more than my current 34,
so something had to be done!


# What's New

So what's new, what I did to convince people to use my extension instead...
or maybe just because once I get started, I just can't seem to stop myself 😀

- Activate directly on websites where it makes sense
- Activate with a click or a (configurable) shortcut
- Support all the badges that seem worthwhile
- Make everything configurable

![The Result - Starify All The Links](/assets/blog-images/starify-all-links.png)


# Thanks Claude

I had to do some things before starting all that because the current code base
was, well, just the one js file.

The things that Claude did for me:
- After I renamed the file to `.ts`, it adjusted the `gulpfile` to convert it to `.js`
  - I did the typescript typings myself, but I'm guessing that Claude could have done this for me too!
- Split the single source file into many files
  - It chose rollup and configured it for me
- Turned my hardcoded shortcut into a configurable `chrome://extensions/shortcuts`
- Turned the extension icon into a "Starify Links" activator
- Added Playwright tests with both fixed html pages as well as tests on actual sites
- Made these tests execute with a Github Action
  - Testing on actual sites didn't work out of the box, but this was fixed with one additional prompt
  - Added a cron job to run the tests daily
- I introduced some design to allow badges other than repository links
  - After that Claude managed to add other badges with me just providing the link to the shields.io badge url
  - It didn't work for all the badges, but it did work straight away for almost all of them

As soon as I asked it for some actual functional implementation, it fell short; the implementations were naive.
I'm not sure if it would be able to figure it out eventually, as at that point I just took over
and coded those parts myself.

The configuration screen was a great success for Claude, it pretty much built it
without me doing any work, and all that in about 10-15 prompts.

But then when I was starting to trust it and just gave it a pretty big prompt and went for coffee,
I came back to "_API Error: 400 due to tool use concurrency issues. Run /rewind to recover the conversation._"
While `/rewind` no longer exists, a simple "_You failed. Can you try to continue where you left off?_" was all it
took to get it back on track!


# What's Next

I ran out of Claude tokens so the `directActivation` options screen is still WIP 😀

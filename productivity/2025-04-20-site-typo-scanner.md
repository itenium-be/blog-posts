---
layout: post
author: Wouter Van Schandevijl
title:  "Site Inspector"
subTitle: "Check broken links and typos"
date:   2025-04-20
desc: >
  There are some todos I bolted down that have been lying around for,
  literally, years. Some for decades even 😀🤦
  This is one of them, and it caught some "interesting" things
  right away.
bigimg:
  url: site-inspector-big.png
  prompt: "A humorous yet sleek digital scene of a magnifying glass scanning a glitchy website, with typos, broken links, and warning symbols popping out, a floating terminal showing gem install siteinspector - ERROR, stylized browser UI, retro terminal vibes, moody lighting, emoji overlays like 🤦 and 😬, modern flat design, tech noir aesthetics, depth of field"
  origin: Midjourney
img:
  url: site-inspector.png
  origin: Midjourney
  prompt: "A cartoon detective inspecting a messy website filled with silly typos and red warning signs, surprised expression, magnifying glass revealing errors, broken chain links, a speech bubble with gem not found!, emojis like 🤦 and 🙈 floating around, bright colors, playful art style, vector flat design"
categories: productivity
interesting:
  - url: https://github.com/siteinspector/siteinspector
    desc: "A tool for catching spelling errors, grammatical errors, broken links, and other errors on websites."
tags: [tutorial]
toc:
  title: 🕵️ TypeScanner
socials:
  linkedin: ""
  instagram: ""
  twitter: ""
  facebook: ""
  youtube: ""
---

While moving some todos from one location to another I came across "Check Site Inspector"
and thought, why not run it against this website right now -- how hard can it be.

{% include github-stars.html url="siteinspector/siteinspector" %}

Not that hard it turns out.

<!--more-->

# 💸💸 Triplechecker

The git repository has been archived since January 2025 and they basically point you towards
[www.triplechecker.com](https://www.triplechecker.com/) which is their cloud deploy.

It worked very well... Finding a whopping 100 spelling errors on itenium.be,
but after the first 7 errors, it asked me to sign up for a mere €15/month. Uhm, no thanks.


# Docker Container

You can pretty easily host it yourself though!

```sh
git clone https://github.com/siteinspector/siteinspector
cd siteinspector
# Open docker-compose.yml, search for "SECRET_KEY_BASE:"
# and enter a random 30+ character key.
docker compose up -d
```

Surf to [http://localhost:808](http://localhost:808)

📈 Profit!!


# Big oopsie

So one of the 100+ typos it found was on our home page,
in the very first slide, a typo which has been screaming
at our customers on our website for **8 years**. Whoops!  
<small>(streng**ht**en vs streng**th**en)</small>

![Strengthening our business](/assets/blog-images/site-inspector-oopsie.png "Strengthening our business"){: .img-responsive}

Heh heh, maybe no one ever noticed? 😬🙈🤫


# Let AI figure it out

Since the siteinspector README just points you towards the paying service,
I had the brilliant idea of asking AI to figure it out for me!

**Prompt**: _https://github.com/siteinspector/siteinspector_  
_You know this one? Know how I can run it against my website?_

Yes! `siteinspector` is a Ruby gem that audits websites for things like redirects, headers, security, and more.
{: .notice--info}

Here's how to run it against your website:

✅ 1. Install Ruby (if needed)  
✅ 2. Install siteinspector gem  
✅ 3. Run it against your site  

```sh
gem install siteinspector
siteinspector https://itenium.be
```

## AI Typosquatting

```
ERROR:  Could not find a valid gem 'siteinspector' (>= 0) in any repository
ERROR:  Possible alternatives: site-inspector
```

That's right, the gem doesn't exist. And `site-inspector` is something different.

{% include github-stars.html url="benbalter/site-inspector" desc="Ruby Gem to sniff information about a domain's technology and capabilities." %}

It **typosquat** me; an emerging danger of AI!  
Let's use the same steps mechanism it used on me to run a potentially disastrous command to illustrate.

✅ 1. AI hallucinates some dependency that looks real.  
✅ 2. A malicious party registers this dependency and puts malware in it.  
✅ 3. An unsuspecting developer runs the command and 💥

---
layout: post
author: Van Schandevijl Wouter
title: ""
subTitle: ""
date: 2025-09-28
categories: docker
tags: [war-story]
bigimg:
  url: -big.png
  desc: "By Midjourney"
  origin: Midjourney
  prompt: ""
img:
  url: .png
  desc: ""
  origin: Midjourney
  title: "By Midjourney"
  prompt: ""
desc: >
  
interesting:
  - url: ""
    desc: ""
extras:
  - githubproject: https://github.com/
    githubtext: ""
toc:
  icon: icon-shell
  title: Shai Hulud
socials:
  linkedin: ""
  twitter: ""
  instagram: ""
  facebook: ""
---

Zie ook email "NX vulnerability" (itenium email) met foto's en meer info.



For Shai-Hulud, let the Jihad begin

 

Create a repository with this vulernability and create a PR that extracts the credentials.

Er was een sessie over Github Actions die ook secrets extracte…

 

 

 

NX (=npm package voor monorepo handling)

https://github.com/nrwl/nx/security/advisories/GHSA-cxm3-wv7p-598c

Er werden malicious packages op npm gepublished (minstens paar duizend gebruikers affected), als je die binnenhaalde, dan was er een postinstall script dat alle .env files ging uitlezen, je environment variables, etc en dat dan naar een webhook sturen.

Daarnaast ook aanpassingen aan vb .bashrc om je systeem af te sluiten wanneer je een prompt opent.

 

De vulnerability werd geintroduceerd door deze Github action:

 

```yaml
- name: Validate PR title
  run: |
     echo "Validating PR title: ${{ github.event.pull_request.title }}"
```

 

Waarbij er een PR gemaakt werd met een title die een script bevat dat dan op de CI uitgevoerd werd

Waardoor de env.NODE_AUTH_TOKEN naar een webhook gestuurd werd:

https://github.com/nrwl/nx/commit/3905475cfd0e0ea670e20c6a9eaeb768169dc33d

Hoe is die vulnerability ingekomen, thanks to AI



<!--more-->


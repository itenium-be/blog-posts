---
layout: post
author: Wouter Van Schandevijl
title: "Security Bootcamp Prep"
subTitle: "Start our game of capture the flag with an edge"
date: 2024-03-16
desc: >
  Things you can already install (and try out?) before the bootcamp starts.
  <br><br>
  Less installing and more hacking on the day itself!
bigimg:
  url: security-bootcamp-prep-big.png
  origin: Midjourney
  prompt: "an underground bunker stuffed to the brim with food and other supplies"
img:
  url: security-bootcamp-prep.png
  desc: "Prepping!"
  origin: Midjourney
  prompt: "a fat computer engineer stretching his hands"
categories: security
tags: [tutorial,hacking]
series: security-bootcamp
toc:
  title: Get Ready!
  icon: eye

competitor:
  ceo: Alexander Cipher
  company: ACME Coding Con
  zip: mitechcon-2024-sessions.zip
ctf-date:
  start: thursday 21 March
  vault: friday 22 March at 11AM
  end: friday 22 March at 5PM
---

The next iteration of the itenium Security Bootcamp is at the [Michigan Technology Conference 2024](https://www.mitechcon.org/). Its vulnerable applications will be running for the duration of the conference: exploit the vulnerabilities (hack!?) and submit captured flags in the Portal website.

Here is how those that will participate can prepare themselves.

If the term `SQL Injection` does not ring a bell, you may want to start by reading up on
that and other vulnerabilities such as: XSS, CSRF, OWASP, ...

<!--more-->

**Super Tips**:  
Statements in this blog that have a 😉 emoji, ignore at your own peril 😉


# API Key

**GUARD YOUR API KEY WITH YOUR LIFE**

Your API key is how the game keeps track of your progress.  
The API key is required to login and to submit captured flags (=score points).

You should have received yours by email.  
If you do not have an API key, contact one of the game admins.


## Teams

**Divide & Conquer**

It's possible to play in a team!


# Start Playing

At the start of the conference we will communicatie how to:

- Connect to our network
- The URL of the Portal

Once you've reached the Portal, you can login with your API key
which will generate a JWT token used for authentication.



# Setting The Stage

**BREAKING NEWS**:
All MITechCon sessions STOLEN, mere days before the conference start!

It is assumed that **{{page.competitor.ceo}}** from **{{page.competitor.company}}** is behind
the theft of the priceless `{{page.competitor.zip}}` (probably because they
had insufficient material of their own).

But don't panic just yet, there is hope still: our hacker liaison discovered {{page.competitor.company}} has scheduled a major portal migration on {{page.ctf-date.start}}. We managed to secure migration API keys, which will enable us to infiltrate their portal and, with some clever hacking, retrieve our beloved zip.


# Your Tools

## Hardware

**Bring your own ethernet cable and make sure your laptop has an ethernet port!**

You do not want to perform a brute force attack on wifi, trust us.


## Software

### Hardcore

You are a seasoned hacker or really want to dive into this.

[Kali linux](https://www.kali.org/get-kali) is an open-source, Debian-based Linux distribution geared towards security tasks, such as Penetration Testing and Security Research.


[Metasploit][metasploit] is the world's most used penetration testing framework.  
You can install this on Windows!


### Docker

There is a Docker image for pretty much all the tools you'll need.

Already install [Docker for Windows](https://docs.docker.com/desktop/install/windows-install/)
and download the Docker images at home!

```sh
docker pull instrumentisto/nmap
docker pull paoloo/sqlmap
docker pull vanhauser/hydra
docker pull adminer
docker pull mongoclient/mongoclient
docker pull rediscommander/redis-commander
docker pull jrottenberg/ffmpeg
```

### Manual Install

- [Postman](https://www.postman.com/downloads/): fabricating requests is necessary for certain hacks!
- Database tools: [Compass][compass] (mongo), [HeidiSQL][heidisql] (mysql), [AnotherRedisDesktopManager][redis] (redis)
- Hacking tools: [nmap][nmap], [Hydra][hydra], [sqlmap][sqlmap], [ffmpeg][ffmpeg]


[compass]: https://www.mongodb.com/try/download/compass
[heidisql]: https://www.heidisql.com/download.php
[Redis]: https://goanother.com/#download
[ffmpeg]: https://ffmpeg.org/
[nmap]: https://nmap.org/download.html
[hydra]: https://sourceforge.net/projects/thc-hydra.mirror/
[sqlmap]: https://sqlmap.org/
[metasploit]: https://www.metasploit.com/download


## Tools

See "ALSO IN THIS SERIES" for our specific blog posts on
using nmap, hydra and sqlmap.


### Chrome Dev Tools

Open Dev Tools with **F12**.  
The following tabs will be interesting for the bootcamp:

- **Application** > "Local Storage"
    - token: Your JWT token
    - user: Your details
- **Network**
    - Many hacks are performed by doing a Http request.
    - The flag is typically in the Response of such request!
    - Interesting options
        - Fetch/XHR: this is what we’re interested in
        - Preserve log: do not purge the list when the page is refreshed
    - Interesting request details
        - Headers > Request Url: to see query string arguments
        - Payload: the body sent with the request
        - Response: the reply from the server
- Console
    - Interesting logs or errors may show up here


You could also monitor network traffic with a tool like
[Fiddler](https://www.telerik.com/download/fiddler-everywhere)
or [Wireshark](https://www.wireshark.org/download.html)


# The Game

## The Portal

If you are unsure how to get started

- Explore The Portal website
    - Make sure to update your settings after logging in 😉
    - Visit the "Hacks Overview" page from the portal for extra tips
- Run an `nmap` scan on the server the Portal is served from to discover more

## In-Game Blogs

As part of the Security Bootcamp, two (vulnerable) Wordpress
blogs are spinned up, visit them from the Portal for more useful information:

**The Hacker's Toolkit Blog** contains posts on hacking tools and general info on JWT, Capturing Flags and more

**The Security Audit Blog** contains clues for the treasure hunts.


## The Flags

Just performing a certain hack does not score points,
you have to locate the flag that is revealed by the hack.

Make sure to check the response body after you have performed a hack 😉

The following case-sensitive regex will match a flag:

```js
[A-Z]{2,5}-[a-zA-Z0-9. -]{4,50}
```

Other things that score points:

- The API key of another player not in your team
- The JWT token of another player not in your team

Check the blog post in the The Hacker's Toolkit Blog ingame for more info!


## The Vault

The Vault is where {{page.competitor.ceo}} has hidden our precious zip. 
It is the end game and will open on {{page.ctf-date.vault}}.  
But you can continue playing until {{page.ctf-date.end}}, when the winners
will be announced.

Many of the flags found in the game are somehow encrypted
with [simple ciphers](https://rumkin.com/tools/cipher/).

Attempt to open the Vault by entering all your decrypted flags
as passphrase and score Vault multipliers.

Beware, incorrectly decrypted flags will cost points!

Check the blog post in the The Hacker's Toolkit Blog ingame for more info!

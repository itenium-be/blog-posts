---
layout: post
author: Wouter Van Schandevijl
title: "Serving a Dockerized web app over https on Synology"
subTitle: "Just clicking through some forms"
date: 2025-06-22
desc: >
  The easiest way to serve an app on your synology over https,
  using the builtin nginx reverse proxy.
bigimg:
  url: synology-https-big.png
  origin: Midjourney
  prompt: "A sleek home server setup with a Synology NAS in the background, a glowing Docker logo floating above it, and a secure HTTPS padlock icon in the foreground. The scene is futuristic, slightly moody lighting, with digital connection lines representing reverse proxy routing to a web application. Techy, modern, clean composition --ar 700:131"
img:
  url: synology-https.png
  desc: ""
  origin: Midjourney
  prompt: "Cartoon-style illustration of a smiling Synology NAS wearing sunglasses, sending HTTPS traffic through a glowing reverse proxy tunnel into a happy Docker whale. Bright, playful colors, comic-style outlines, fun and approachable vibe"
categories: docker
tags: [synology]
interesting:
  - url: https://mariushosting.com/synology-https-ssl-to-your-docker-containers-without-port-at-the-end/
    desc: "Marius Hosting: https and app.your-syno.synology.me (with screenshots!)"
toc:
  title: Https
  icon: server
socials:
  linkedin: ""
  twitter: ""
  facebook: ""
---

You've developed the most fancy application and are running
it on your Synology in a Docker container.

But you really don't want that ugly "Not secure" when opening
it. This series goes over your options, starting with the
easiest one to setup: using the Synology nginx.

<!--more-->

Open Synology DSM and

# Step 1: Setup Reverse Proxy

Control Panel > Login Portal > Advanced > Reverse Proxy > Create

Reverse Proxy Rules:
- Pick a "Reverse Proxy Name"
- Source
  - Protocol: HTTPS
  - Hostname: *
  - Port: _some port_
  - Check "Enable HSTS"
- Destination
  - Protocol: HTTP
  - Hostname: localhost
  - Port: _port exposed by your docker container_
- Save

# Step 2: Create Certificate

Control Panel > Security > Certificate > Add

- Add a new certificate > Next
- Enter a "Description"
- Get a certificate from Let's Encrypt
- Domain name: _your-syno.synology.me_
- Email: _your email_

You can reuse the same certificate for multiple Docker containers.

# Step 3: Use Certificate

Control Panel > Security > Certificate > Settings

- Service: As defined in Step 1 Source Hostname:Port
  - ex: _*:some port_
- Certificate: Select the Certificate created in Step 2

`https://your-syno.synology.me:some_port` should now work!

This configuration is written to `/etc/nginx/sites-enabled/server.ReverseProxy.conf`

---
layout: post
author: Wouter Van Schandevijl
title: "Hacking Tool: nmap"
subTitle: "The free Network Mapper"
date: 2023-10-01
desc: >
  Scan all ports on a server to figure out what services are running.
bigimg:
  url: hacking-nmap-big.png
  desc: ""
  origin: Midjourney
  prompt: "the nmap logo looking down on vulnerable servers"
img:
  url: hacking-nmap.png
  desc: ""
  origin: Midjourney
  prompt: "Eye with computer design concept on graph in digital environment electronic signaling technology, cad( computer aided design) , neon grids, grid work, luminous 3d objects, in the style of daz3d, 3d illustration, projection, circuit boards, city in eye, clock eye, computer chips, fractal"
  title: ""
categories: security
tags: [tutorial,hacking]
series: security-bootcamp
toc:
  title: nmap
  icon: fa-eye
package-versions:
  - package: nmap
    version: 5.2
---

Before Trinity could take down the electric grid, she had to find an attack vector. Her hack started with... [nmap](https://nmap.org/)!

<br>

{% include post/youtube.html id="0PxTAn4g20U" desc="And then used an actual SSH exploit. Nmap can obviously do more than tell you whether SSH is on port 22 ;)" %}

<!--more-->

# What

A CLI tool to discover hosts and services on a computer network
by sending packets and analyzing the responses.

If you rather have a GUI, there is [zenmap](https://nmap.org/zenmap/).


# How

List all bells & whistles:

```sh
nmap --help
```


## Fast Scan

<i class="fa fa-hourglass-half"></i> +/- 10s

```sh
nmap -sT -T4 -Pn 10.23.10.2
```

This lists all ports that have something running.  
The service name displayed is the default port of a known service
for the port but there is no telling if that is actually the case.

Flags used:

- `-sT`: [Scan Technique](https://nmap.org/book/man-port-scanning-techniques.html): TCP connect scan, will show up in server logs.
    - `-sS`: Quick (thousands/sec), unobtrusive and stealthy with clear, reliable differentiation between the open, closed, and filtered states. The default and most popular.
    - `-sU`:  UDP scans
- `-T<0-5>`: Set [timing template](https://nmap.org/book/performance-timing-templates.html) (higher is faster)
    - paranoid (0) & sneaky (1): Enable IDS (Intrusion Detection System) evasion
    - polite (2): Don't overload to the target machine
    - normal (3): (default)
    - aggressive (4): for fast and reliable networks
    - insane (5): for extraordinarily fast networks
- `-Pn`: Treat all hosts as online -- skip host discovery



## Slow Scan

<i class="fa fa-hourglass-half"></i> +/- 10m

```sh
nmap -sT -T4 -Pn -sV -O -p- 10.23.10.2
```

Flags used:

- `-sV`: Probe open ports to determine service/version info
- `-O`: Enable OS detection
- `-p <port ranges>`: Only scan specified ports


## Docker

There is an Docker image for that…

```sh
docker run --rm -it instrumentisto/nmap -A -T4 scanme.nmap.org
```

Flags used:

- `-A`: Enable OS detection, version detection, script scanning, and traceroute


# When

At the start of the itenium Security Bootcamp at least one player per team should do
an nmap scan to discover services that are not (yet) available directly from the Portal.

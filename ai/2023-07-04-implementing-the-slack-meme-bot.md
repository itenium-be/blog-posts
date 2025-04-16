---
layout: post
author: Van Schandevijl Wouter
title: "The itenium Slack Meme Bot® : Implementation"
subTitle: "How was the Slack Meme Bot implemented?"
date: 2023-07-04
categories: javascript
tags: [product,fun]
bigimg:
  url: slack-meme-bot-big.png
  desc: "By Midjourney"
  origin: Midjourney
  prompt: "memes on social media --ar 2:1"
img:
  url: slack-meme-bot2.png
  desc: "Actual footage of our meme bot!"
  origin: Midjourney
  title: "By Midjourney"
  prompt: "the slack meme bot"
desc: >
  Technical implementation details for the Slack Meme Bot
series: slack-meme-poster
toc:
  icon: slack
  title: Slack Meme Bot
---

How does the Slack Meme Bot work, technically?

<!--more-->

# docker-compose

The `docker-compose` exists of two services

## File Server

`halverneus/static-file-server` makes the memes accessible from the internet.  
The Slack WebHook sends just the url of the meme, after which Slack attempts to
download the image before actually posting it.

If Slack cannot download the image, **nothing** will show up in your Slack channel.

You will probably need to configure the meme file accessibility.

```sh
chmod -R 755 memes_dir
```

## Cron Job

The `Dockerfile` copies the `job` folder with the Node program to the docker container
and then runs `start.sh` which schedules running `meme-poster.js` and starts the Cron daemon. 

### Slack Meme Poster

The `meme-poster.js` selects a random file from your `MEMES_DIR` and moves it to
a `already-sent` subfolder to make sure no meme is ever posted twice.

It then sends the meme url to Slack using the WebHook and 🎉, meme posted!


# Make it your own

[Configure the actual Slack message](https://github.com/itenium-be/slack-meme-poster/blob/master/job/post-slack.js)

- `messageToPostWithMeme`: A simple string sent along with the meme
- Configure the entire Slack message using the Slack [Block Kit Builder](https://app.slack.com/block-kit-builder/)

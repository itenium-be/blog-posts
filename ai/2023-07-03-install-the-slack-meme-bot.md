---
layout: post
author: Van Schandevijl Wouter
title: "The itenium Slack Meme Bot® : Installation"
subTitle: "Use your meme collection to liven op your Slack channel"
date: 2023-07-03
categories: javascript
tags: [product,fun]
bigimg:
  url: slack-meme-bot-big.png
  desc: "By Midjourney"
  origin: Midjourney
  prompt: "memes on social media --ar 2:1"
img:
  url: slack-meme-bot.png
  desc: "Actual footage of our meme bot!"
  origin: Midjourney
  title: "By Midjourney"
  prompt: "the slack meme bot"
desc: >
  Share your meme collection on Slack by posting a random meme
  at certain times with a scheduled job.
series: slack-meme-poster
toc:
  icon: slack
  title: Slack Meme Bot
---

You want to post memes to Slack but who has time for that?  
Time to automate this to avoid scrolling through your entire collection whenever
it is time to post one!


<!--more-->

# What it looks like

![Example of a Slack Meme Bot posting]({{ site.baseurl }}/assets/blog-images/slack-meme-bot-example.png "Slack Meme Bot in action")

# Requirements

{% include github-stars.html url="itenium-be/slack-meme-poster" desc="Install your own Slack Meme Bot now!" %}

- A huge meme collection
- Docker
- Slack

That's it, really!

# Installation & Configuration

See [README.md](https://github.com/itenium-be/slack-meme-poster) for step by step instructions.

```sh
git clone https://github.com/itenium-be/slack-meme-poster
cd slack-meme-poster

# Configure Slack webhook etc
cp .env.sample .env

# Schedule meme-posting!
docker-compose up --build -d
```

The `.env` contains:
- Location of your memes
    - Forbidden? `chmod -R 755 memes_folder`
- The Slack WebHook url (see below for instructions)
- Cron expression
    - The default will post a meme once every friday
    - Let the [crontab.guru](https://crontab.guru/) configure this for you


# Slack WebHook Setup

Get yourself a Slack WebHook url, which looks like this:

```sh
https://hooks.slack.com/services/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
```

Follow their [official instructions](https://api.slack.com/messaging/webhooks):

- [Create An App](https://api.slack.com/apps?new_app=1)
- Features > Incoming Webhooks
    - Activate Incoming Webhooks
    - Add New Webhook to Workspace
    - Copy the url!

![Activating Slack Webhook]({{ site.baseurl }}/assets/blog-images/slack-meme-bot-slack-setup.png "Activating Slack Webhook"){: .img-responsive}

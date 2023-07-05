---
layout: post
author: Van Schandevijl Wouter
title: "The itenium Slack Meme Bot® : with AI?"
subTitle: "Implementation was easy enough. Is ChatGPT up for the task?"
date: 2023-07-05
categories: javascript
tags: [product,fun]
bigimg:
  url: slack-meme-bot-big2.png
  desc: "By Midjourney"
  origin: Midjourney
  prompt: "chatgpt posting funny programming memes on social media --ar 2:1"
img:
  url: slack-meme-bot3.png
  desc: "Added 'written by chatgpt' to the prompt and I got a worse looking bot ;)"
  origin: Midjourney
  title: "By Midjourney"
  prompt: "a slack meme bot written by chatgpt"
desc: >
  Can ChatGPT create a Slack Meme Bot?
extras:
  - githubproject: https://github.com/itenium-be/slack-meme-poster-ai
    githubtext: "Some of the code that ChatGPT created"
series: slack-meme-poster
toc:
  icon: slack
  title: Slack Meme Bot
---

A few hours of fun, playing around with Docker, Cron, Node, Shell and a FileServer.
Can ChatGPT do this too? And maybe in less time?

To be honest, I am very biased against ChatGPT but still hoping, very hard, that it can just do this,
so that I can start using it for simple stuff, POCs and more!

<!--more-->

We're talking a whole whopping 180 lines of code here, including newlines and comments.
ChatGPT is all the hype, so surely it must be able to write this very very simple app, right?

# Initial Prompt

Let's dumb it down a bit, forgetting about Docker, the static file server and the `already-sent` folder for now.


> Hello there ChatGPT. Could you create a program for me that, every friday at 16 hours selects a random image from a directory and sends it to a Slack channel?


It created a small Python script with inline configuration for Slack API token and meme directory.
And then suggested using `cron` or `Task Scheduler` for the friday 16h part. That's actually a good
idea, I should add Windows instructions to the installation blog post for non-Docker usage 😃


Anyway, let's try it out!

```sh
pip install slack-sdk
python 1-InitialPrompt.py
```

Well, that didn't quite work:

```py
  File ".\1-InitialPrompt.py", line 26
    print(f"Image sent to Slack channel '{channel}' successfully!")
                                                                 ^
SyntaxError: invalid syntax
```

Uhoh, still using Python 2.7 in 2023, let's quickly update my `$env:PATH`.


The full script:

```py
import os
import random
import glob
from slack_sdk import WebClient
from slack_sdk.errors import SlackApiError

SLACK_API_TOKEN = "<your-slack-api-token>"
IMAGE_DIRECTORY = "/path/to/your/image/directory"
slack_channel = "#your-channel-name"

slack_client = WebClient(token=SLACK_API_TOKEN)

def send_image_to_slack(channel, file_path):
    try:
        response = slack_client.files_upload(
            channels=channel,
            file=file_path,
            title="Random Image",
        )
        print(f"Image sent to Slack channel '{channel}' successfully!")
    except SlackApiError as e:
        print(f"Failed to send image to Slack channel '{channel}': {e.response['error']}")


image_files = glob.glob(os.path.join(IMAGE_DIRECTORY, "*.jpg"))
random_image = random.choice(image_files)
send_image_to_slack(slack_channel, random_image)
```

The main reason I used the WebHook in the first place because, well getting an API to work...

- Fixed a warning by replacing `slack_client.files_upload` to `_v2` with single channel
- And then a barrage of API errors
    - not_allowed_token_type: `xapp` tokens vs `xoxp` and `xoxb` etc
    - missing_scope: needed OAuth scopes: `files:write`, ...
    - not_in_channel
    - channel_not_found: `/invite @MemeBot` did not solve this nor did `chat:write.public`

At this point I was a bit at a loss, the channel most surely exists and I added the bot to it.
So... Let's ask ChatGPT?


> What Slack OAuth scopes do I need to upload a file to a channel using the API?


It gave me some additional scopes but they seemed irrelevant and I indeed got the same error after.


# WebHook Prompt

Let's go back to the WebHook then..

> Could you generate code using a Slack Webhook instead?


Which resulted in code that can actually send a message to Slack.
But not with a picture... It used `attachments: []` but that doesn't actually work.
Even if it did, you would still need something to serve the files...

The program:

```py
import os
import random
import glob
import requests

SLACK_WEBHOOK_URL = "https://hooks.slack.com/services/..."
IMAGE_DIRECTORY = "./memes"

def send_image_to_slack(webhook_url, file_path):
    try:
        payload = {
            "text": "Random Image",
            "attachments": [
                {
                    "image_url": file_path
                }
            ]
        }
        response = requests.post(webhook_url, json=payload)
        response.raise_for_status()
        print(f"Image sent to Slack successfully!")
    except requests.exceptions.RequestException as e:
        print(f"Failed to send image to Slack: {str(e)}")

image_files = glob.glob(os.path.join(IMAGE_DIRECTORY, "*.jpg"))
random_image = random.choice(image_files)

send_image_to_slack(SLACK_WEBHOOK_URL, random_image)
```

It did manage to fix this with an additional prompt!

> Could you generate a Slack Webhook payload with text and an image?

Which generated a working payload:

```
payload = {
    "text": "Random Image",
    "attachments": [
        {
            "fallback": "Image",
            "image_url": file_path
        }
    ]
}
```

Progress!! 🎉



# File Server

> Can you create a docker-compose with a service that executes a Python script with a cron job and a static file server service?

This created
- `docker-compose.yml`: Used nginx to create a working static file server
- `Dockerfile`: Took a different approach to what I ended up with
- `cronjob`: It even remembered I wanted to run this on fridays at 16h!

It was time to integrate the two solutions. Some manual fixes and additional
prompts later, it had generated something that seemed like it would work.

> Could you replace the python-script.py with the one you generated earlier?

> Can we bring this all together?  
> A docker-compose that schedules a cron job that selects a random image from a mounted volume and sends the url as served by an nginx service to Slack using a webhook?

> You didn't include the Python script...  
> This does not use the docker-compose variables you used in your earlier answer  
> The dockerfile should do a pip install of requests  


However it did not actually run the cron job... No logs to be found, it just sat there.

Here it seemed I either ran against the limits of ChatGPT or against the limitations
of my "AI Prompt Engineering"-skillz.


## Backtrack: Hello World

I then tried for over an hour to get ChatGPT to create a docker-compose that
prints "Hello World".

> Let's try again:
> Can you create a docker-compose that schedules a cron job to print "Hello World" every minute?

Some of the things it tried:
- `crontab` file with `echo`
- `docker-compose` with `Command`
- `startup.sh` to print Hello World instead
- ...
- Back to a Python script
- The `vixiecron` package

At this point I was just copy pasting the code and nothing worked.


## Being Sassy

> Do you think that if I keep asking changes that at some point it will actually work?

*... While I do my best to understand and assist with your requests, troubleshooting specific technical issues can be challenging without having direct access to your environment. ...*

And here is me, thinking that the entire philosophy of Docker is to not be dependent on the developer environment.
Oh and I'm also asking for a freaking "Hello World" app here... 



# Troubleshooting

ChatGPT pretty much gave up after a while and shifted into troubleshooting mode.

It came up with some interesting suggestions, most of which I had already tried at that point.

```sh
docker logs <container_id_or_name>

docker exec -it <container_id_or_name> sh
python /app/python-script.py
cat /etc/cron.d/cronjob
service cron status
cat /var/log/syslog
```


# Conclusion

It is a gadget, nothing more.

I felt like I was asking questions to a junior developer who copy pasted code
from somewhere and handed it over without any testing. Everything it does
needs to be reviewed thoroughly. To me, it doesn't seem like it can be used
for much more than generating some skeleton code, saving you a few keystrokes.


## The Horror

During technical interviews, I sometimes come across what I call a "Trial & Error"-programmer:
They try something and then run the code, check if the result is correct and if it is not,
they try something different. Rinse and repeat.

I hope that ChatGPT and the like are not raising a generation of Trial & Error programmers
because that is exactly how I am feeling after copy pasting code for a few hours and making
small adjustments here and there.

I quit this exercise the moment it spewed out the following:

```py
while True:
  schedule.run_pending()
  time.sleep(1)
```

> Is it a good idea to do that?

*You raise a valid concern. Using an infinite loop with a sleep statement in the main script might not be the most efficient approach, as it can consume unnecessary resources.*

After which it generated code without scheduling 😃


### Reading vs Writing

> “Indeed, the ratio of time spent reading versus writing is well over 10 to 1. We are constantly reading old code as part of the effort to write new code. ...[Therefore,] making it easy to read makes it easier to write.”
> ― Robert C. Martin, Clean Code: A Handbook of Agile Software Craftsmanship

Or how about this one:

> "Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it." - Brian W


ChatGPT is not helping you by generating code you do not understand, it is just making
debugging and future feature development that much harder, if it succeeded in
producing something that works.


## The Good

If you already know how to code and want to have 80% of your code generated for you,
you may want to have a look at using ChatGPT. Just remember that:

- It spits out random bugs, even in things that it got correct earlier (ex: "Image" -> "Random Image" in the Slack webhook payload)
- It generates code and configuration that it does not actually use


One thing that did impress me was that after I told it a few times "Nope, also doesn't work",
it gave some pretty good recommendations on how to debug and troubleshoot its (broken) solutions.


It seems to be quite good at solving really small very well known problems. I always forget the exact syntax for
`docker exec`. A simple prompt gave me a correct copy-pastable answer! That is to be expected considering how it
works beneath the hood I guess.

## Introspection

From ChatGPT itself:

*Programming requires creativity, problem-solving skills, and a deep understanding of various technologies. While I can offer guidance, suggestions, and code examples, it's important to remember that human programmers bring their unique expertise, experience, and insights to the table. The human element is crucial in the programming process, and no AI can replicate the entirety of human skills and knowledge.*

**Please consider me as a tool that can assist you in your programming journey, providing information, ideas, and helping with specific tasks.** *However, the ingenuity and skill of human programmers remain essential for developing robust and innovative solutions.*

*If you have any further questions or need assistance with any programming topic, feel free to ask. I'm here to help in any way I can!*


It was unable to tell me if that response was hardcoded or not.


## Final Thoughts

I'm disappointed, for sure. It created a working Python script quite fast which got my hopes up,
but then it failed at what is typically the hardest part anyway: integration.

I will be using this to generate small things on the side. Like my Bash skills are below par,
I'm sure ChatGPT can give me something that already mostly works.

Oh man, I feel old, bitter and out of touch now.

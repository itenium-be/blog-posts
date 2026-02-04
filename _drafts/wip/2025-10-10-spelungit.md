---
layout: post
author: Wouter Van Schandevijl
title: "Querying your git history"
subTitle: "Now with 100% more AI thanks to Spelungit"
date: 2025-10-10
desc: >
  Spelungit: When `git log --grep` isn't enough
bigimg:
  url: spelungit-big.png
  origin: Midjourney
  prompt: ""
img:
  url: spelungit.png
  desc: ""
  origin: Midjourney
  prompt: ""
categories: dev-setup
tags: [git]
interesting:
  - url: https://github.com/haacked/Encourage
    desc: "A bit of encouragment added to Visual Studio -- another Haacked product!"
toc:
  title: Spelungit
  icon: icon-git
socials:
  linkedin: ""
  twitter: ""
  facebook: ""
---

I'm a big fan of Phil Haack's blog, the quirky style, I love it.

Recently he blogged about a new product he created, named
[Spelungit](https://haacked.com/archive/2025/09/29/announcing-spelungit)

{% include github-stars.html url="haacked/spelungit" desc="An MCP server for searching git history using natural language" %}

Searching git history, it's also something that I find myself doing quite often.

<!--more-->


# Git Aliases

## Log

The most basic way to search git history is with `git log`, you can then use `/`
and type a regex to search for something (use `n`/`N` for next/previous matches).  
See [Less for your git pager]({{ '/blog/dev-setup/git-core-pager-less-pager/#searching-1' | relative_url}})
for more details on searching the logs.

The default `git log` is a quite verbose output however, so git aliases to the rescue!

```ini
# Or: --date=iso8601 if you don't like relative
l = log --color --abbrev-commit --graph --date=relative --pretty=format:'%C(magenta)%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cd)%C(bold blue)<%an>%Creset'

# To see which files have changed how much
ll = log --decorate --date=relative --numstat --pretty=format:'%C(magenta)%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cd)%C(bold blue)<%an>%Creset'
```

### Usage

```sh
# Just the last 5 commits
git l -5

# If you know by whom
git l --author=Haacked

# If it's not part of your current branch
git l --all
```



## Finding Changes

For simple searches where you know exactly what to search for, I have these aliases:

```ini
# Find commits by commit message
fm = "!f() { git log --pretty=format:'%C(yellow)%h  %Cblue%ad  %Creset%s%Cgreen  [%cn] %Cred%d' --decorate --date=short -i --grep=\"$*\"; }; f"

# Find commits by source code
fc = "!f() { git log --pretty=format:'%C(yellow)%h  %Cblue%ad  %Creset%s%Cgreen  [%cn] %Cred%d' --decorate --date=short -i -S\"$*\"; }; f"
```

### Usage

```ps1
git fm TFS ID 12345

git fc "fn_name("
```

## Reflog

Another way to search history is to list where HEAD was pointing to last.
This alias lists the reflog with the timestamp the commit/checkout/... command happened.

```ini
refl = reflog --date=relative --pretty=format:'%C(magenta)%h%Creset -%C(yellow)%d%Creset %gs %s %Cgreen(%cd)%C(bold blue)<%an>%Creset'

# With the exact date (instead of relative)
refld = !git refl --date=format-local:'%Y-%m-%d %H:%M:%S'
```


# Git Blame

Opening a file and seeing for each line when it was last changed, it's something that is available
in every self-respecting IDE.

In Visual Studio Code, for example with "[GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens): Toggle File Blame"

{% include github-stars.html url="gitkraken/vscode-gitlens" desc="Supercharge Git inside VS Code" %}

![File Blame](/assets/blog-assets/git-history-blame.png){: .img-responsive }


# Spelungit

But... Once you don't know the exact wording of the commit message or the function name,
git falls short. Or maybe because your colleagues don't
[exercise proper git hygiene]({{ '/blog/productivity/git-pull-request-hygiene/' | relative_url }})
it might be quite hard to find exactly what you're looking for, so 🥁🥁 introducing Spelungit!



## Spelunky

Spelunky is a legendary **roguelike platformer** where you explore randomly generated caves, each run unique and unpredictable. With permanent death and endless surprises, it's a thrilling treasure hunt filled with traps, monsters, and secrets — a masterpiece of emergent gameplay and procedural storytelling.


To setup: https://glama.ai/blog/2024-11-25-model-context-protocol-quickstart


Installing Claude Desktop:
https://www.reddit.com/r/ClaudeAI/comments/1jiffk6/why_bother_installing_claude_for_desktop/


## GitLens

When creating the screenshot for the GitLens `git blame`, I noticed that GitLens has actually
implemented exactly the same thing! But it's only available for the non-free version 😡


# Alternatives

## Github MCP Server

https://github.com/github/github-mcp-server
https://github.blog/ai-and-ml/generative-ai/a-practical-guide-on-how-to-use-the-github-mcp-server/

## Git MCP

https://gitmcp.io/ --> https://github.com/idosal/git-mcp


## Git MCP Server

https://github.com/cyanheads/git-mcp-server
--> Template: https://github.com/cyanheads/mcp-ts-template


# SeeGit

This is not the first git related product from Phil, over 10 years ago he created "SeeGit".

{% include github-stars.html url="haacked/seegit" desc="SeeGit - The Git Repository Visualizer" %}

That is more a fun type of project though, it isn't very useful in and by itself 😀

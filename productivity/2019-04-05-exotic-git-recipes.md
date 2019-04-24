---
layout: post
author: Wouter Van Schandevijl
title:  "Exotic Git Snippets"
date:   2019-04-05 00:00:00 GMT-2
desc: >
  Some not-so-often used git commands.
img:
  url: git-snippets.jpg
  origin: https://unsplash.com/photos/Bb_X4JgSqIM
  desc: Photo by Brine Blum
categories: productivity
tags: [git]
interesting:
  - url: https://stackoverflow.com/a/21079437/540352
    desc: "Ignore certain files in git output"
toc:
  title: Git Recipes
  icon: icon-git
---

Some git commands and scripts that come in handy from time to time.


<!--more-->


# Splitting Repositories

You start with a small project. It grows over time as you add more and more features.
And then, at some point, you realize one of these features should actually
have been a separate repository because it provides functionality that can be useful
in multiple contexts.

So far I moved the files and did a `git init`. At this point, I was always sad
because well, I lost the history of the feature. Not only less
commits for my Github statistics but I also lost `git blame` so it could no longer
tell me why a particular algorithm was written the way it was.

Turns out git allows you to have your cake and eat it too!

```
git filter-branch --prune-empty --subdirectory-filter src master
```


<br>
# Locking out package-lock.json

These days each `npm install` makes a `git diff` on frontend projects a real drag.
Page upon page of uninteresting changes.

```
git diff -- ':!*package-lock.json' ':!*yarn.lock'
```

Or as a `~/.gitconfig` alias:
```
df = "!f() { git diff --ignore-all-space -- $1 ':(exclude)*yarn.lock' ':!**package-lock.json'; }; f"
```


<br>
# Deleting merged branches

**Locally**:  
`~/.gitconfig` aliases. `(develop|master)`: Do not delete these branches even if merged with current branch.
```ini
merged-branches = !git branch --merged | egrep -v \"(\\*| ) (develop|master)\" | xargs -n 1
dm = !git merged-branches git branch -d
```

**Remote**:  

Somehow a CI server always seems to be amassing feature branches. A simple cleanup script to the rescue!

Replace `name-comitter` (author filter) and `origin` (remote filter) and add as a `~/.gitconfig` alias:  
```ini
my-merged-remote-branches = !git for-each-ref --format='%(authorname):%(refname)' | egrep \"name-comitter\" | egrep \"refs/remotes\" | sed -e \"s/^.*:refs\\/remotes\\/origin\\//:/\"
```

Followed by PowerShell:  
```powershell
git my-merged-remote-branches | % { git push --no-verify origin $_ }
```


<br>
# Filename case renaming

Pesky Windows.  
Also note the [`core.ignorecase`](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coreignoreCase) configuration value.
```
git mv -f OldFileNameCase newfilenamecase
```


<br>
<br>
# Other blog posts

- [git assume unchanged](https://itenium.be/blog/productivity/git-assume-unchanged/)
- [git hooks with Husky](https://itenium.be/blog/productivity/git-hooks-with-husky/)

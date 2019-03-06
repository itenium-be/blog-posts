---
layout: post
author: Wouter Van Schandevijl
title:  "Git Assume Unchanged"
date:   2019-02-18 00:00:00
desc: >
  "git assume unchanged" any (configuration) file you need to modify 
  but also want to avoid committing/pushing accidently.
img: git-assume-unchanged.jpg
interesting:
  - desc: "Stackoverflow: --assume-unchanged vs --skip-worktree"
    url: https://stackoverflow.com/questions/13630849/git-difference-between-assume-unchanged-and-skip-worktree
categories: productivity
tags: [git]
toc:
  title: Assume Unchanged
  icon: icon-git
---

Ideally one would create a new gitignored file to configure an application to fit his local development
environment but when you do have to revert to modifying a committed configuration file, at least there is
"git assume unchanged" to avoid committing these files accidently.


# Assume Unchanged
{: .hide-from-excerpt}

```powershell
# Git assume a file
git update-index --assume-unchanged fileName

# And unassume with
git update-index --no-assume-unchanged fileName
```

<!--more-->

## Aliases

But who can remember that...  
Some handy git aliases without `update-index` nightmarish syntax to be put in your `~/.gitconfig`:

```ini
[alias]
    # Usage: git assume fileName
    assume = update-index --assume-unchanged

    # Usage: git noassume fileName
    noassume = update-index --no-assume-unchanged

    # Another alias because I cannot remember un/no either
    unassume = !git noassume

    # List all currently git assumed files
    assumed = "!git ls-files -v | grep ^h | cut -c 3-"
```

Or create the aliases from the CLI:  
```powershell
git config --global alias.assume "update-index --assume-unchanged"
git config --global alias.noassume "update-index --no-assume-unchanged"
git config --global alias.unassume "update-index --no-assume-unchanged"
git config --global alias.assumed "!git ls-files -v | grep ^h | cut -c 3-"
```

One of our other projects [Git-NumberedAdd](/blog/productivity/git-numbered-add-for-powershell) allows you to execute these commands with fabricated
indexes instead of filenames.


# Skip Worktree

I've always used assume unchanged but apparently `--skip-worktree` would be a superior
solution for changes in configuration files because it retains the `S` flag (as seen with `git ls-files -v`)
when pulling upstream changes. I'll find out as upstream changes are a real pain with assumed unchanged.

<!-- TODO: is skip-worktree really better when dealing with changes on origin? -->

```powershell
git update-index --skip-worktree fileName
git update-index --no-skip-worktree fileName
```

## Aliases

```ini
[alias]
    hide = update-index --skip-worktree
    unhide = update-index --no-skip-worktree
    hidden = "!git ls-files -v | grep ^S | cut -c 3-"
```

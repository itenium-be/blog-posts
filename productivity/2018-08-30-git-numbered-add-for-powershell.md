---
layout: post
author: Wouter Van Schandevijl
title:  "Git-NumberedAdd for PowerShell"
date:   2018-08-30 15:00:00
desc: >
  Tired of copying <code>git status</code> output for <code>git add</code> input for the millionth time?
  Despair no more for <code>Git-NumberedAdd</code> is here!
img: git-add-numbered-add.jpg
categories: productivity
tags: [git,powershell]
extras:
  - githubproject: https://github.com/itenium-be/Git-NumberedAdd
    githubtext: Git-NumberedStatus, Add, Diff and Reset ps1 source
interesting:
  - desc: Similar implementation in Bash
    git: scmbreeze/scm_breeze
  - desc: Similar implementation in Perl
    git: holygeek/git-number
toc:
  title: Numbered Add
  icon: icon-git
---

After copying file paths from `git status` output and pasting them after a `git add` quite a few times
by now, I've written a small [PowerShell script](https://github.com/itenium-be/Git-NumberedAdd)
to stage the files by index.

<!--more-->

# Git-NumberedStatus <small>(alias: gs)</small>

Displays the output of `git status --short` together with fabricated indexes like so

![Git-NumberedStatus](/assets/blog-images/git-add-numbered-status.png){: .img-responsive}
 
Working directory color codes:
- Yellow: <span style="color: #CDCB7D">**M**</span>odified (file0 to file3)
- DarkMagenta: <span style="color: #8C5998">**D**</span>eleted (file4)
- Blue: <span style="color: #0480EF">**A**</span>dded (file5)

<br>
Configure the colors by modifying `$global:gitStatusNumbers`.

## With Numstat

If `$global:gitStatusNumbers.includeNumstat` is true, Git-NumberedStatus will also
execute a `git diff --numstat` and add lines added/deleted to the output.

![Git-NumberedStatus with --numstat](/assets/blog-images/git-add-numbered-status-numstat.png){: .img-responsive}


# Git-NumberedAdd <small>(alias: ga)</small>

All set to stage some files!

```ps
PS> Git-NumberedAdd 0 1 2
add 'file0'
add 'file1'
add 'file2'
```

The same can be achieved with:  
- `Git-NumberedAdd 0-2`
- `Git-NumberedAdd -3`
- `Git-NumberedAdd 012` (but only if there is a max of 10 files in the git status)

<br>
`Git-NumberedAdd +3` would add file4 and file5 in the above scenario.



# Git-NumberedDiff <small>(alias: gd)</small>

Works like `Git-NumberedAdd` but can also be called without arguments to
see the diff of all files. It will call `git add -N` for all newly created
files so that they also show up in the diff.

`git diff --cached` is currently not implemented in this script.

# Git-NumberedReset <small>(alias: grs)</small>

This only works for files already staged.
Example output:  

![Git-NumberedStatus](/assets/blog-images/git-add-numbered-status-staged.png){: .img-responsive}

To, for example unstage file0 and file4  
```ps
PS> Git-NumberedReset 02
Unstaged changes after reset:
M       file0
M       file1
M       file2
D       file4
```

# Alternatives

Sometime similar is actually built into Git itself
```bash
git add --interactive # or -i for short
```

Once in interactive mode:  

![git add --interactive](/assets/blog-images/git-add-interactive.png){: .img-responsive}

And then use `2: Update` to stage files.

<br>
Also, see below for similar implementations in Bash and Perl.

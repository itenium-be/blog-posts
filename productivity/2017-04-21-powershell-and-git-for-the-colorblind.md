---
layout: post
author: Wouter Van Schandevijl
title:  "PowerShell and Git for the colorblind"
date:   2017-04-21 15:00:00 +0200
categories: productivity
tags: [powershell,git]
toc:
  title: Colorization
  icon: icon-git
updates:
  - date: 2018-08-22 00:00:00 +0200
    desc: Update for Posh-Git v1
---

My PowerShell, Posh-Git and .gitconfig color configuration because of some red-green troubles
with the default configurations.

<!--more-->

> in the land of the blind, the one-eyed man is king


PowerShell
==========

List current colors
```powershell
$host.PrivateData |
	Get-Member -MemberType Property |
	Select-Object Name,@{label='Value';exp={$host.PrivateData.($_.Name)}} |
	Format-Table -AutoSize
```

With just changing how Errors are displayed, everything is readable, for me, with Cmder.

```powershell
$host.PrivateData.ErrorBackgroundColor = 'Red'
$host.PrivateData.ErrorForegroundColor = 'Yellow' # or: "DarkMagenta"
```


* * *


Posh-Git
========

List current configuration
```powershell
$GitPromptSettings
```
### v0.x

My changes
```powershell
$global:GitPromptSettings.WorkingForegroundColor = [ConsoleColor]::Yellow
$global:GitPromptSettings.BranchForegroundColor = [ConsoleColor]::Green
```

Get possible colors  
```powershell
[ConsoleColor].GetEnumNames()
```

- Gray, Blue, Green, Cyan, Red, Magenta, Yellow, White
- Black, DarkBlue, DarkGreen, DarkCyan, DarkRed, DarkMagenta, DarkYellow, DarkGray

### v1.x

Some breaking changes...  
```powershell
$GitPromptSettings.DefaultPromptPath.ForegroundColor = 'White'

$GitPromptSettings.LocalWorkingStatusSymbol.ForegroundColor = 'Yellow'
$GitPromptSettings.WorkingColor.ForegroundColor = 'Yellow'

$GitPromptSettings.BranchAheadStatusSymbol.ForegroundColor = 'Green'
$GitPromptSettings.BranchBehindStatusSymbol.ForegroundColor = 'DarkMagenta'
$GitPromptSettings.BranchBehindAndAheadStatusSymbol.ForegroundColor = 'Yellow'
```

Colors are interpreted with  
```powershell
System.Drawing.ColorTranslator.FromHtml()
```


* * *


Git
===
List current colors
```powershell
git config --list | Where-Object {$_ -Like "color*"}
```

**~/.gitconfig**

Available colors: normal, black, red, green, yellow, blue, magenta, cyan, or white  
Second optional color param values: bold, dim, ul (underline), blink, and reverse (swap foreground and background)

```ini
[color "branch"]
	current = yellow
	local = white
	remote = magenta

[color "status"]
	header = white
	changed = magenta bold
	untracked = magenta bold
	unmerged = magenta reverse
	added = yellow
	branch = white

[color "diff"]
	meta = blue white
	frag = normal bold
	old = magenta bold
	new = green bold
```

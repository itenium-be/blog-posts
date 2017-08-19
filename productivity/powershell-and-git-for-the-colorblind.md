---
layout: post
title:  "PowerShell and Git for the colorblind"
date:   2017-04-21 15:00:00 +0200
categories: productivity
tags: [powershell,git]
---

{% include toc title="Colorization" icon="eye" %}

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
List current colors
```powershell
$global:GitPromptSettings |
	Get-Member |
	Where-Object { $_.Name.Contains("Color") }
```

My changes
```powershell
$global:GitPromptSettings.WorkingForegroundColor = [ConsoleColor]::Yellow
$global:GitPromptSettings.BranchForegroundColor = [ConsoleColor]::Green
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

```git
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

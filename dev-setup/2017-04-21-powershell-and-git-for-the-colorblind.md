---
layout: post
author: Wouter Van Schandevijl
title:  "PowerShell and Git for the colorblind"
date:   2017-04-21 15:00:00 +0200
categories: dev-setup
img:
  url: colorblind.png
bigimg:
  url: colorblind-big.png
  prompt: "colorblind"
  origin: Midjourney
tags: [powershell,git]
toc:
  title: Colorization
  icon: icon-git
last_modified_at: 2024-07-08 00:00:00 +0200
updates:
  - date: 2024-07-08 00:00:00 +0200
    desc: Added Show-Colors function
  - date: 2018-08-22 00:00:00 +0200
    desc: Update for Posh-Git v1
redirect_from: /blog/productivity/powershell-and-git-for-the-colorblind/
---

My PowerShell, Posh-Git and .gitconfig color configuration because of some red-green troubles
with the default configurations.

<!--more-->

> in the land of the blind, the one-eyed man is king


PowerShell
==========

List current colors

```powershell
function Show-Colors() {
  Write-Host "Available Colors:"
  $colors = [Enum]::GetValues([ConsoleColor])
  $max = ($colors | foreach { "$_ ".Length } | Measure-Object -Maximum).Maximum
  foreach ($color in $colors) {
    Write-Host (" {0,2} {1,$max} " -f [int]$color,$color) -NoNewline
    Write-Host "$color" -Foreground $color
  }

  Write-Host ""
  Write-Host "Current Configuration:"
  $host.PrivateData |
    Get-Member -MemberType Property |
    Select-Object Name,@{label='Value';exp={$host.PrivateData.($_.Name)}} |
    Format-Table -AutoSize

  Write-Host ""
  Write-Host "Current Configuration (visualized):"
  $colors = $host.PrivateData
  Write-Host Error -Back $colors.ErrorBackgroundColor -Fore $colors.ErrorForegroundColor
  Write-Host Warning -Back $colors.WarningBackgroundColor -Fore $colors.WarningForegroundColor
  Write-Host Debug -Back $colors.DebugBackgroundColor -Fore $colors.DebugForegroundColor
  Write-Host Verbose -Back $colors.VerboseBackgroundColor -Fore $colors.VerboseForegroundColor
  Write-Host Progress -Back $colors.ProgressBackgroundColor -Fore $colors.ProgressForegroundColor
}
Show-Colors
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


# Advantages

![Nailed it!](/assets/blog-assets/colorblind-ididit.png)

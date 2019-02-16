---
layout: post
author: Wouter Van Schandevijl
title:  "PowerShell profiles"
desc:   Where to find and how to reload the different PowerShell profiles.
date:   2017-04-11 20:00:00 +0200
img: powershell-profiles.png
categories: productivity
tags: [powershell,windows]
toc:
  title: PowerShell Profiles
  icon: icon-shell
lastUpdate: 2018-09-04 01:04:05 +0200
updates:
  - date: 2018-09-04 01:04:05 +0200
    desc: Updated locations for PowerShell Core
---

Reload PowerShell profile
```powershell
. $profile
```

<!--more-->

# PowerShell Core

The default location changed in PowerShell Core to  
```powershell
$home\Documents\PowerShell
```


# PowerShell


Profile locations
```powershell
# executes automatically
$home\Documents\WindowsPowerShell\profile.ps1

# executes with console host only
$home\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1

# executes with ISE only
$home\Documents\WindowsPowerShell\Microsoft.PowerShellISE_profile.ps1

# Visual Studio Package Manager Console
$home\Documents\WindowsPowerShell\NuGet_profile.ps1
```

When `. $profile` doesn't quite cut it.

```powershell
function Reload-Profile {
	@(
		$Profile.AllUsersAllHosts,
		$Profile.AllUsersCurrentHost,
		$Profile.CurrentUserAllHosts,
		$Profile.CurrentUserCurrentHost
	) | % {
		if (Test-Path $_) {
			Write-Verbose "Reloading $_"
			. $_
		}
	}
}
```

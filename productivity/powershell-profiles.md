---
layout: post
title:  "PowerShell profiles"
date:   2017-04-11 20:00:00 +0200
categories: productivity
tags: [powershell,windows]
---

Where to find and how to reload the different PowerShell profiles.

<!--more-->

Reload PowerShell profile
```powershell
. $profile
```

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

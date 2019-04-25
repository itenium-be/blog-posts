---
layout: post
author: Wouter Van Schandevijl
title:  "Getting PowerShell help"
date:   2019-02-26 15:00:00
categories: dev-setup
tags: [powershell]
img:
  url: 
extras:
  - url: https://learnxinyminutes.com/docs/powershell/
    desc: Learn Powershell in Y minutes
toc:
  title: PowerShell Help
  icon: icon-shell
---

https://www.sconstantinou.com/useful-powershell-commands/

To see the examples, type: "get-help Get-ChildItem -examples".
For more information, type: "get-help Get-ChildItem -detailed".
For technical information, type: "get-help Get-ChildItem -full".
For online help, type: "get-help Get-ChildItem -online"


```powershell
# Download the help (run as admin)
Update-Help

# Shows: Syntax, description, parameters
# Alias: man
help Get-Process

Get-Help Get-Process -Examples

# Alternatively
Get-Process -?
```

Get-Members
Get-Help

help about_*

<!--more-->

```
# alias help, man
Get-Help


```


# Aliases

Aliases are displayed with `Get-Help`

```powershell
Get-Alias dir
Get-Alias -Definition Get-ChildItem
```


Get-Command

```powershell
# Find commands
Get-Command about_* # alias: gcm
Get-Command -Verb Add


Get-Help ps | less # alias: help
ps | Get-Member # alias: gm

Show-Command Get-EventLog # Display GUI to fill in the parameters


```








# Metadata
Get-Alias, New-Alias
Get-Alias -Definition Get-Process

$PSVersionTable
$PSScriptRoot
$env:psmodulepath
$env:path
$pshome

$PSDefaultParameterValues

$Get-PSRepository, ...

# Built in variables
# TODO: $NULL, $false, ...

# Built in help
Get-Help *log* -Full
Get-Help about_*
Get-Command -Noun *event*
(Get-Command Get-Eventlog | select -ExpandProperty parameters).computername.aliases
Show-Command Get-Eventlog # Display GUI to fill in the parameters
Get-Service | Get-Member

Update-Help # Run as admin















function Git-NumberedHelp() {
    $table = Get-Command -Name "Git-*" | ? { $_.CommandType -eq 'Function' } | % {
        $name = $_.name
        $help = get-help $_ -Full
        $example = If ($help.Examples) {$true} Else {''}
        $alias = Get-Alias -Definition $name -ErrorAction SilentlyContinue

        return @{name=$name;description=$help.Synopsis;example=$example;alias=$alias}
    }

    $table.ForEach({[PSCustomObject]$_}) | Format-Table -AutoSize
}


##############################################################################
#.SYNOPSIS
# Display the results of Parse-GitStatus
#
#.DESCRIPTION
# Output looks like:
# ---------------------
# Staged files:
#   0  M file0
#   1  A file1
#
# Working directory:
#   0  M file0
# ---------------------
#
# Legend
# '0-1' are the indexes
# Status codes:
# - M: Modified
# - A: Added
# - D: deleted
# - R: Renamed
#
# Use Git-NumberedHelp for an overview of all actions and utilities
#
#.EXAMPLE
# Git-NumberedStatus
#
# And follow with:
# Git-NumberedAdd -3 5 6 8-10
# To add the files: 0, 1, 2, 5, 6, 8, 9 and 10
##############################################################################

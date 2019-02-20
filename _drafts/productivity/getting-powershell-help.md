---
layout: post
author: Wouter Van Schandevijl
title:  "Getting PowerShell help"
date:   2017-04-12 15:00:00 +0200
categories: productivity
tags: [powershell]
---

Get-Alias -Definition Get-ChildItem

Get-Members
Get-Help

help about_*

<!--more-->

Get-Command




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

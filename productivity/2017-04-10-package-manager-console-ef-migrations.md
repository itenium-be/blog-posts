---
layout: post
title:  "Package Manager Console Entity Framework 6 Migrations profile"
date:   2017-04-10 20:22:13 +0200
categories: productivity
tags: [net,ef6,powershell,nuget]
extras:
  - githubproject: https://github.com/Laoujin/dotfiles/blob/master/config/shells/NuGet_profile.ps1
    githubtext: "The full Nuget_profile.ps1"
---

{% include toc title="EF6 PMC" icon="dot-circle-o" %}

This tutorial is about adding the following functionality to the Entity Framework Migrations CLI:  
- Execute commands against the project containing the migrations by providing the most likely -Project parameter value
- Allow updating the database using a numeric TargetMigration (-2 = migrate to the third last migration)

<!--more-->





Activating your Nuget_profile
-----------------------------
When opening the Package Manager Console (PMC) in Visual Studio, the profile file is executed.

```cmd
%USERPROFILE%\Documents\WindowsPowerShell\NuGet_profile.ps1
```

You can reload the NuGet_profile.ps1 file using `. $profile` in the PMC.

The following aliases allow you to install a package with just `ip EntityFramework`:  
```powershell
Set-Alias ip Install-Package
Set-Alias unip Uninstall-Package
```


* * *


Provide most likely -Project parameter
--------------------------------------
There is (unfortunately) no way to configure the "Default project" in the PMC. This workaround creates a Verb-RealNoun
for each existing cmdlet and adds a short alias to these commands. Which project contains the migrations is by convention,
as determined by `$isLikelyDbContextProject`.

```powershell
# Execute commands against first ProjectName to end with one of these:
$isLikelyDbContextProject = ".DataAccess", ".Back"

function Get-DbContextProject {
	$dbContextProjects = Get-Project -All | Where-Object { `
		$isLikelyDbContextProject -match $_.ProjectName.Substring($_.ProjectName.LastIndexOf(".")) `
	}
	return $dbContextProjects[0]
}

function Add-RealMigration($name = "test") {
	$project = Get-DbContextProject
	Add-Migration $name -Project $project.ProjectName
}

Set-Alias am Add-RealMigration

# Usage
am AddedBlogTable
```

The entire list of aliases:  
```powershell
Set-Alias em Enable-RealMigrations
Set-Alias am Add-RealMigration
Set-Alias ad Add-RealMigration
Set-Alias ud Update-RealDatabase
Set-Alias udv Update-RealDatabaseVerbosely
Set-Alias gam Get-AppliedMigrations
Set-Alias lm List-Migrations # List last $listMigrationsCount
Set-Alias lam List-AllMigrations
```


* * *


Updating the database using a numeric TargetMigration
-----------------------------------------------------
Easily go back to a specific migration using `ud -1` instead of
```powershell
Update-Database -TargetMigration:SecondLastMigrationName
```

### Usage
```powershell
# List last 5 migrations:
# (aliased as List-Migrations or lm)
$ Get-MigrationsTable | Select-Object -First 5 | Format-Table -AutoSize

Index Name                   Created          FullName  
----- ----                   -------          --------  
0     MatchShouldBePlayed    12/24/2016 16:25 201612241625590_MatchShouldBePlayed.cs  
-1    MatchFormationComment  12/24/2016 14:34 201612241434105_MatchFormationComment.cs  
-2    UserPasswordResetLinks 10/23/2016 18:53 201610231853333_UserPasswordResetLinks.cs  
-3    ClubManagerTypes       09/23/2016 20:46 201609232046188_ClubManagerTypes.cs  
-4    BlockMatchField        08/18/2016 21:40 201608182140266_BlockMatchField.cs  

# Update database to ClubManagerTypes
$ Update-RealDatabase -3 # Alias: ud -3

# Update database to latest migration (MatchShouldBePlayed)
$ Update-RealDatabase 0 # Alias: ud
```

**Attention**:  
This completely ignores whatever may be the last migration that was applied to the database.
Having `Update-RealDatabase -1` go back to the "last *applied* migration - 1" instead of the current implementation
"go to the second last migration added to the project", would require analysing `Get-Migrations`, which would make the whole
thing considerably slower. (FileSystem vs Sql query)

### Source
```powershell
function Get-MigrationsTable {
	$project = Get-DbContextProject
	$projectPath = Split-Path -Path $project.FullName

	$migrations = Get-ChildItem "$projectPath\Migrations" |
		Where-Object { Test-MigrationName $_.Name } |
		Sort-Object Name -Descending

	return $migrations | ForEach-Object -Begin {$idx = 0} -Process {
		$withoutExt = $_.Name.Substring(0, $_.Name.LastIndexOf("."))
		$migrationName = $withoutExt.Substring($withoutExt.IndexOf("_") + 1)
		$dateStr = $_.Name.Substring(0, $_.Name.IndexOf("_"))
		$date = [DateTime]::ParseExact($dateStr.Substring(0, 12), "yyyyMMddHHmm", $null)

		$_ | Select-Object -Property `
			@{l='Index';e={"$idx"}}, `
			@{l='Name'; e={$migrationName}}, `
			@{l='Created'; e={$date.ToString("g")}}, `
			@{l='FullName'; e={$_.Name}}

		$idx -= 1
	}
}

function Update-RealDatabase([int]$targetMigration = 0) {
	$project = Get-DbContextProject
	if ($targetMigration -eq 0) {
		# Default: Update to latest migration
		Update-Database -Project $project.ProjectName
	} else {
		# With negative number: Go to x last created migration
		$migration = Get-MigrationsTable | Where-Object {$_.Index -eq $targetMigration}
		echo "Migrating to last migration $targetMigration => $($migration.name)"
		Update-Database -Project $project.ProjectName -TargetMigration $migration.name
	}
}

function Test-MigrationName($fileName) {
	return $fileName -match "^(\d[^.]+)\.(cs|vb)$"
}
```


* * *


Open the Package Manager Console in Visual Studio
-------------------------------------------------
Menu: Tools > NuGet Package Manager > Package Manager Console  
Shortcut: Alt + T, N, O  
Custom shortcut: View.PackageManagerConsole  


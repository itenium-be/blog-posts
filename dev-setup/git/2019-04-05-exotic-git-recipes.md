---
layout: post
author: Wouter Van Schandevijl
title:  "Exotic Git Snippets"
date:   2019-04-05 00:00:00 GMT-2
desc: >
  Some not-so-often used git commands.
img:
  url: git-snippets.jpg
  origin: https://unsplash.com/photos/Bb_X4JgSqIM
  desc: Photo by Brine Blum
categories: dev-setup
tags: [git]
interesting:
  - url: https://stackoverflow.com/a/21079437/540352
    desc: "Ignore certain files in git output"
toc:
  title: Git Recipes
  icon: icon-git
redirect_from: /blog/productivity/exotic-git-recipes
last_modified_at: 2024-06-05 00:00:00 +0200
updates:
  - date: 2024-06-05 00:00:00 +0200
    desc: git filter-repo and Create-PullRequest for Github
  - date: 2019-12-30 00:00:00 +0200
    desc: PS scripts to remap remote urls & create a PR
---

Some git commands and scripts that come in handy from time to time.


<!--more-->


# Splitting Repositories

You start with a small project. It grows over time as you add more and more features.
And then, at some point, you realize one of these features should actually
have been a separate repository because it provides functionality that can be useful
in multiple contexts.

So far I moved the files and did a `git init`. At this point, I was always sad
because well, I lost the history of the feature. Not only less
commits for my Github statistics but I also lost `git blame` so it could no longer
tell me why a particular algorithm was written the way it was.

Turns out git allows you to have your cake and eat it too!

```bash
# DEPRECATED!!
git filter-branch --prune-empty --subdirectory-filter src master
```

These days you get a deprecation warning when using `filter-branch`
and are pointed to a third party package instead:

{% include github-stars.html url="newren/git-filter-repo" desc="Quickly rewrite git repository history (filter-branch replacement)" %}

[git-filter-repo(1) docs](https://htmlpreview.github.io/?https://github.com/newren/git-filter-repo/blob/docs/html/git-filter-repo.html)


```sh
# Requires git >= 2.24.0 and Python >= 3.5
pip3 install git-filter-repo
git filter-repo -h
git filter-repo --path src --subdirectory-filter src
```


<br>
# Locking out package-lock.json

These days each `npm install` makes a `git diff` on frontend projects a real drag.
Page upon page of uninteresting changes.  

```bash
git diff -- ':!*package-lock.json' ':!*yarn.lock'
```

Or as a `~/.gitconfig` alias:
```bash
df = "!f() { git diff --ignore-all-space -- $1 ':(exclude)*yarn.lock' ':!**package-lock.json'; }; f"
```

Important note: If you use the `git df` alias, `git df package-lock.json` will **never** show anything.
You'll have to use plain old `git diff` for checking the excluded files!

<br>
# Deleting merged branches

**Locally**:  
`~/.gitconfig` aliases. `(develop|master)`: Do not delete these branches even if merged with current branch.
```ini
merged-branches = !git branch --merged | egrep -v \"(\\*| ) (develop|master)\" | xargs -n 1
dm = !git merged-branches git branch -d
```

**Remote**:  

Somehow a CI server always seems to be amassing feature branches. A simple cleanup script to the rescue!

Replace `name-committer` (author filter) and `origin` (remote filter) and add as a `~/.gitconfig` alias:  
```ini
my-merged-remote-branches = !git for-each-ref --format='%(authorname):%(refname)' | egrep \"name-committer\" | egrep \"refs/remotes\" | sed -e \"s/^.*:refs\\/remotes\\/origin\\//:/\"
```

Followed by PowerShell:  
```powershell
git my-merged-remote-branches | % { git push --no-verify origin $_ }
```


<br>
# Filename case renaming

Pesky Windows.  
Also note the [`core.ignorecase`](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coreignoreCase) configuration value.
```bash
git mv -f OldFileNameCase newfilenamecase
```


<br>
# Remapping remote urls

When the remote url changes for all your repositories.

```ps1
$paths = Get-ChildItem "c:\git-repos" | ? { $_.PSIsContainer }
foreach ($path in $paths) {
    $gitPath = Join-Path $path.FullName "\.git"
    if (Test-Path -Path $gitPath -PathType Container) {
        Write-Host $path.FullName " -> " $gitPath
        $url = "https://new-base-url.com/$($path.Name)"
        Write-Host "Remap to $url"
        Push-Location $path.FullName
        git remote set-url origin $url
        Write-Host " "
        Pop-Location
    }
}
```


<br>
# Create PR in browser

From your current branch.

```ps1
function Create-PullRequest() {
  $baseUrl = git remote get-url origin

  if ($baseUrl -match 'github.com') {
    $urlTemplate += "{base-url}/compare/{target-branch}...{source-branch}"
  } else {
    $urlTemplate += "{base-url}/pullrequestcreate?sourceRef={source-branch}&targetRef={target-branch}"
  }

  $sourceBranch = git rev-parse --abbrev-ref HEAD
  # $targetBranch = "develop"
  $targetBranch = "master"

  git push -u origin $sourceBranch
  start $urlTemplate.Replace("{base-url}", $baseUrl).Replace("{source-branch}", [uri]::EscapeDataString($sourceBranch)).Replace("{target-branch}", $targetBranch)
}

Set-Alias pr Create-PullRequest
```




<br>
<br>
# Other blog posts

- [git assume unchanged](https://itenium.be/blog/dev-setup/git-assume-unchanged/)
- [git hooks with Husky](https://itenium.be/blog/productivity/git-hooks-with-husky/)

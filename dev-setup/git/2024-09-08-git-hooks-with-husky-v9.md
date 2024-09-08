---
layout: post
author: Wouter Van Schandevijl
title: "Git Hooks with Husky v9"
subTitle: "Avoid pushing changes that break the build with githooks and Husky."
date: 2024-09-08
desc: >
  Lint, test, fix, build, ... before committing or pushing changes
  with git hooks and Husky v9.
bigimg:
  url: git-hooks-husky-v9-big.png
  origin: Midjourney
  prompt: "huskies running in the snow"
img:
  url: git-hooks-husky-v9.png
  desc: "Modern native Git hooks made easy"
  origin: Midjourney
  prompt: "husky git hooks"
categories: dev-setup
tags: [git]
extras:
  - githubproject: https://github.com/itenium-be/Frontend-Track
    githubtext: "Template code repository"
interesting:
  - url: https://git-scm.com/docs/githooks
    desc: Official githooks documentation
toc:
  title: Git Hooks
  icon: icon-git
package-versions:
  - package: husky
    version: 9.1.5
redirect_from: /blog/productivity/git-hooks-with-husky/
---

{% include github-stars.html url="typicode/husky" desc="Git hooks made easy 🐶 woof!" %}

<!--more-->

# Hooks

> **git hooks**  
> Run a program before certain points in gits execution.  
> `pre-commit` and `pre-push` hooks are probably the most interesting.

They are defined in `.git/hooks`.
You can find some example files in that directory. Remove their `.sample` suffix
to test them out.

The githooks are saved locally and not pushed to the remote. This is obviously a problem
when working in a team but the `.git/hooks` folder can be changed globally or for the current repository
with the [`core.hooksPath`](https://git-scm.com/docs/git-config#Documentation/git-config.txt-corehooksPath)
setting.

This is obviously never going to happen but you could skip githooks with:

```sh
git commit --no-verify
git push --no-verify

# Or if you want to really quickly skip it ;)
git commit -n
```


# Husky

Or, use Husky to easily make the git hooks part of the repository itself and share it with the entire team.

```sh
npm install husky --save-dev
npx husky init
```

This will add a `prepare` npm script in your `package.json`. The prepare script runs after
an `npm install` to ensure things are setup for everyone.


```json
"scripts": {
  "prepare": "husky"
},
```

And it creates a `.husky/pre-commit` which you can try out by doing a `git commit`!  
Avoid having to `git reset HEAD~` all the time by ending your script with `exit 1` while
testing/setting it up.

Finally it sets the `core.hooksPath` to `.husky/_`. Husky creates files in that
directory to link all githooks with the `h` shell script which will execute a
`.husky/SOME_GITHOOK` if it exists.  
Husky also adds a gitignore to the `_` folder to keep the generated files out
of your git repository.

```sh
git config core.hooksPath
# --> .husky/_
```

## Script `h`

In this order:

- If `HUSKY=2` it executes `set -x` to print commands with arguments
- If the `.husky/XXX` file does not exist (with XXX for example `pre-commit`), the script exits
- If `~/.config/husky/init.sh` (or `$XDG_CONFIG_HOME/husky/init.sh`) exists, it executes it
  - This is the place to setup `PATH`, or set an environment variable like `NVM_DIR`
- If `HUSKY=0` the script exits
- It adds `node_modules/.bin` to the `PATH`
- It executes `.husky/XXX` (stops on first error)


## CI/CD

You may not be installing the `devDependencies` during a production build.
To avoid the `prepare` npm script from failing:

```json
"scripts": {
  "prepare": "husky || true"
},
```


## Project Not in Git Root Directory

Prepush script that works around the frontend project `package.json` not being in the
root of the git repository.

Set the `package.json` script manually and then do an `npm install`.

```json
"scripts": {
  "prepare": "cd .. && husky frontend/.husky"
},
```

The script with some path juggling:

```bash
#!/bin/sh

# Path Juggling
originalPath=`pwd`
if [ "`git rev-parse --show-cdup`" != "" ]; then cd `git rev-parse --show-cdup`; fi
cd ./frontend-project-folder/

# Linting
npm run lint
if [ $? -ne 0 ]; then
    cd $originalPath
    exit -1
fi

# Testing
npm test
if [ $? -ne 0 ]; then
  cd $originalPath
  exit -1
fi

# Build
npm run build
hasBuildErrors=$?

cd $originalPath
exit $hasBuildErrors
```


# Alternatives

{% include github-stars.html url="toplenboren/simple-git-hooks" desc="A simple git hooks manager for small projects" %}
{% include github-stars.html url="evilmartians/lefthook" desc="Fast and powerful Git hooks manager for any type of projects" %}
{% include github-stars.html url="pre-commit/pre-commit" desc="A framework for managing and maintaining multi-language pre-commit hooks" %}

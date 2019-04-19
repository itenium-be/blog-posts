---
layout: post
author: Wouter Van Schandevijl
title:  "Git Hooks with Husky"
date:   2019-04-13
desc: >
  Lint, test and try a production build before pushing changes
  with git hooks and Husky.
img: git-hooks-husky.jpg
imgurl: https://unsplash.com/photos/jY_2XG-6HU0
imgdesc: Photo by Simon Rae
categories: productivity
tags: [git]
interesting:
  - url: https://git-scm.com/docs/githooks
    desc: Official githooks documentation
toc:
  title: Git Hooks
  icon: icon-git
todo:
 - desc: Use lerna to do the monorepo handling?
   url: https://github.com/lerna/lerna
package-versions:
  husky: 1.3.1
---

Avoid pushing changes that break the build with githooks and Husky.

{% include github-stars.html url="typicode/husky" desc="🐺 Git hooks made easy" %}

<!--more-->

# Hooks

> **git hooks**  
> Run a program before certain points in git's execution.

They are defined in `.git\hooks`. <!-- (config: `core.hooksPath`) -->  
You can find some example files in that directory. Remove their `.sample` suffix
to test them out.


# Husky

Hooks are saved locally and not pushed to the remote.
Husky allows you to easily make the git hooks part of the repository itself.

```
npm install husky --save-dev
```

Add to `package.json`
```json
{
  "husky": {
    "hooks": {
      "pre-push": "bash prepush.sh",
      "pre-commit": "npm run lint"
    }
  }
}
```

It's also possible to put the json (without the `husky` node)
in a `.huskyrc` or `.huskyrc.json` file.


## prepush.sh

Prepush script that works around the frontend project `package.json` not being in the
root of the git repository.

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

## Other

More control with `.huskyrc.js` and environment variables.

```
const tasks = arr => arr.join(' && ');

module.exports = {
  'hooks': {
    'pre-commit': tasks([
      'echo $HUSKY_GIT_PARAMS',
      'echo $HUSKY_GIT_STDIN',
      'echo $HUSKY_DEBUG',
      'echo $HUSKY_SKIP_INSTALL'
    ])
  }
};
```

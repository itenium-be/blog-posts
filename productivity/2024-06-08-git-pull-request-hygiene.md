---
layout: post
author: Wouter Van Schandevijl
title:  "Git Pull Request Hygiene"
date:   2024-06-08
desc: >
  Think about the reviewer:
  keep your PR as small as possible
  and avoid PR comments that you would
  have catched yourself easily by
  just quickly glancing over your
  changes.
bigimg:
  url: git-hygiene-big.png
  prompt: "poor hygiene"
  origin: Midjourney
img:
  url: git-hygiene.png
  origin: Midjourney
  prompt: "git hygiene"
categories: productivity
tags: [git,war-story]
toc:
  title: PR Hygiene
  icon: icon-git
---

It seems that pretty much everyone is using
some sort of UI for their git interactions.

Which is obviously fine, until they are about to create a
commit, in which case it's using the "add all changes"-button
and commit.

Good PR hygiene starts with keeping your commits small and
focused: check your changes and exclude everything that
is not part of the UserStory per se.

That's why I advocate the use of `git add --patch` which gives
you fine-grained control of exactly what you're going to
commit!

<!--more-->

You basically want to avoid forcing your colleagues to review
mega PRs for what could have been much smaller, right?

![Everyone loves reviewing such PR!](/assets/blog-images/git-hygiene-pr.png){: .img-responsive}

These either result in your PR being open for a very long time
(with all the merge conflicts that entails!)
or the reviewer losing patience/focus and just approving
after the first 20 files -- both are outcomes that are not ideal.

# PR Preparation

## Step 1: Stage your changes

```sh
# Shorter
git add -p

# Or even shorter with an alias
git config --global alias.adp 'add -p'
```

**git add --patch**  
Interactively choose hunks of patch between the index and the work tree and add them to the index. This gives the user a chance to review the difference before adding modified contents to the index.
{: .notice--info}

Note that this will not add new files,
you still have to add those with a `git add new_file`!

For each hunk you have a few options, the most common:

| Key     | Meaning                                                 
|---------|---------------------------------------------------------
| y/n     | Add or don't add the hunk
| a/d     | Add all hunks in the file or skip to the next file
| s       | Split the hunk into smaller ones
| e       | Decide manually on a line-per-line basis
| q       | Quit. Whenever it turns out there is still some work left!
{: .table-code}



## Step 2: Review the staging area

Before doing the actual commit, go over all staged
changes: are you about to commit everything that
is needed, and nothing more?

```sh
git diff --staged

# With an alias of course!
git config --global alias.dfc 'diff --staged'
```



## Step 3: Commit!

![Commit messages matter!](/assets/blog-images/git-hygiene-commit-message.jpg){: .img-responsive}

Don't write your life's story (at least not in a commit message!)
but do add something meaningful, what exactly are you changing?
Are you fixing a bug, improving performance, doing source-code cleanup,
a refactoring or are you implementing a new feature?

I always try to get the entire team onboard for **starting each commit message with a ticket ID**
so that there is always a link back to your bug tracking system or Scrum
board. These artifacts are typically long-lived and may contain
additional information like a link to the analysis, comments from
team members, file attachments etc!




## Step 4: Create the PR

You obviously also want to automate this!
See our blog post on [exotic git snippets](https://itenium.be/blog/dev-setup/exotic-git-recipes/#create-pr-in-browser)
for a small script to create a PR like a breeze.

```powershell
# Creating a PR from the commandline:
pr
```

### Check the "Files changed"

Since a PR typically exists of multiple commits,
before sending the PR to your colleague, make
sure to go through all your changes one last time.


### Consider a PR checklist

Most systems allow you to create a file like
[`.github/pull_request_template.md`](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository)
where you can add a checklist of things that are recurring
comments from reviewer(s) that could've
been easily catched by the PR creator.



# Poor Hygiene Examples

## Lingering debug statements

The PR still contains statements
that a quick glance would've catched:

```ts
console.log('val', someValue);
debugger;
// TODO: xyz
```


You made some changes to speed up your manual testing,
that is OK but make sure you don't commit this!!

```ts
function isLoggedIn() {
  // return !!this.userService.token;
  return true;
}
```


## Should never have been committed

Earlier this week I deleted an HTML file
which contained local test run results.

Deleting such files is not enough, create a PR
to add the folder to your `.gitignore` so that
this can never happen again!


## Avoid Superfluous Changes

These are changes that typically happen automatially on a save
and means that someone has a different linter installed,
or that they are running their linter with different settings 😲

```ts
-  constructor(private socksService: SocksService) {}
+  constructor(private socksService: SocksService) { }
```

Another classic, with one keystroke you can add missing imports
but of course your default IDE configuration is different from
the style that is used in 99% of the rest of the code base.

Fix your configuration or fix the styling, please!

```ts
import { Review } from './sock.model';
+import {TimeAgoPipe} from '../time-ago.pipe';
```

It's cool to have an [`.editorconfig`](https://editorconfig.org/) but if not all devs
have the plugin installed in their IDE, it is pretty useless:

```ts
{%raw%}-}
+}{%endraw%}
\ No newline at end of file
```

Same for some extra whitespace at the end of a line: `<router-outlet />`
vs `<router-outlet />  `. Please don't 🥺


Note that a git hook, for example with [Husky](https://itenium.be/blog/dev-setup/git-hooks-with-husky/),
can pretty much solve this in your team by automatically fixing all linting issues right before
a commit.


## Not Needed After All

If your first hunk is this one and there are no
others in the same file, I guess you moved the
code to somewhere else and forgot to clean up
your import statements!

```ts
+import { LOCALE_ID } from '@angular/core';
```

You added a new package but didn't end up actually using it
or ended up using another one? Remove the dependency!

```json
"dependencies": {
+  "cool-package-i-did-not-use-in-the-end": "1.0.0",
+  "@types/cool-package-i-did-not-use-in-the-end": "1.0.0",
}
```

Also note that dev stuff should be in `devDependencies`!



## Local Configuration

Manual or automatic changes for local dev configuration should never be
checked in! That db connectionString on your local machine has no place
being in the official repo. Check our blog post on 
[Git Skip Worktree](https://itenium.be/blog/dev-setup/git-assume-unchanged/#skip-worktree)
how you can make it easier to avoid this.

You are also opening up your project for a merge conflict on pretty much
every PR until eternity...

```json
// PR from colleague A
"cli": { "analytics": false }

// PR from colleague B
"cli": { "analytics": "guid" }
```


## Generated and Useless

`ng generate`... So cool, so easy, and so many useless files
that all get checked in.

For each generated component there is a `component.scss` file. And
even if there is not a single styling rule added, obviously
the file gets checked in.

This is more an issue of "writing vs reading code". Code
is written once, and then read many times over. Do you want
to spend time opening empty files, just to find out it is
indeed, empty? I know I don't.

The other useless file is the `.spec.ts`:

```ts
describe('MyPipe', () => {
  it('creates an instance', () => {
    const pipe = new MyPipe();
    expect(pipe).toBeTruthy();
  });
});
```

This test will, literally, **never** fail.
The `new` operator works, I guarantee it!

So either delete the file, or, even better,
actually write some tests for it!! Especially
as is the case here,
[writing tests for an Angular Pipe](https://itenium.be/blog/javascript/angular-pipes/#testing)
is very straight-forward!

Another thing the code generator seems to be
doing from time to time, re-order statements:

```ts
-  imports: [NgIf, AsyncPipe, TitleCasePipe],
-  templateUrl: './sock.component.html'
+  templateUrl: './sock.component.html',
+  imports: [NgIf, AsyncPipe, TitleCasePipe, CurrencyPipe],
```

This makes the PR look bigger than it really is,
and also harder to spot what has actually changed.


# Conclusion

Keep your PR short & sweet, a mere glance through all
changed files will spot almost all these things.

It allows **you** to fix them instead getting comments from
the PR reviewer, that I would say are a bit embarassing,
especially when it happens time and time again...


Here's one last meme to keep the mood light:

![Yup, pretty much every dev!](/assets/blog-images/git-hygiene-commit-message2.jpg)

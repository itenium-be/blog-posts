---
layout: post
author: Wouter Van Schandevijl
title: "Pragmatic Tip #22 : Use A Single Editor Well"
subTitle: "A Sublime Goodbye"
date: 2019-07-18 00:00:00 +0200
desc: >
  Blog post series on all things VSCode I encountered
  during my switch from Sublime Text 3.
bigimg:
  url: vscode-sublime-view.png
  desc: "Photo by Scott Webb"
  origin: https://unsplash.com/photos/PMq_b18cN6A
img:
  url: vscode-screenshot.png
  desc: "Some fancy rainbow IDE I ended up with 🌈 🌈"
categories: dev-setup
tags: [cheat-sheet,tutorial,pragmatic-tips]
series: vscode
extras:
  - url: https://gist.github.com/Laoujin/eb201494af87c818a3756206352d69ba
    desc: "VSCode Settings Sync Gist"
interesting:
  - git: viatsko/awesome-vscode
    desc: "A curated list of delightful VS Code packages and resources."
  - url: https://marketplace.visualstudio.com/vscode
    desc: "VSCode Marketplace"
toc:
  title: Visual Studio Code
  icon:
package-versions:
  - package: vscode
    version: 1.36.0
todo:
  - desc: "Record Macro's"
    url: https://github.com/Microsoft/vscode/issues/4490
  - desc: "Bug with Markdown All in one extension and Autohotkey hotstrings (workaround {Sleep 250})"
    url: https://github.com/yzhang-gh/vscode-markdown/issues/200
---


![VSCode Editor](/assets/blog-images/vscode-screenshot.png){: .webfeedsFeaturedVisual }


> Pragmatic Tip 22:
> The editor should be an extension of your hand;
> make sure your editor is configurable, extensible,
> and programmable.

As a long time Sublime user, the infrequent updates and missing support for new-ish stuff
started to become quite apparent. Like all plugin maintainers have turned to Visual Studio Code.

And so I finally decided to leave Sublime Text 3 behind. Welcome Visual Studio Code, the new goto editor.

<!--more-->


# Getting Started

```bash
choco install vscode
```

{% include kbd k="Control+K,Control+R" l="Help: Keyboard Shortcuts Reference (pdf)" %}

<br>
{% include kbd k="Control+Shift+P" l="Show All Commands, and follow with:" %}
- [`Help: Documentation`](https://code.visualstudio.com/docs)
- `Help: Interactive Playground`: Learn about refactoring, formatting, snippets and more
- [`Help: Introductory Videos`](https://code.visualstudio.com/docs/getstarted/introvideos)
- [`Help: Tips and Tricks`](https://code.visualstudio.com/docs/getstarted/tips-and-tricks)


# Configuration

## Locations

VSCode stores your configuration in `%APPDATA%\Code\User`:
- `settings.json`
- `keybindings.json`
- `snippets`

Per workspace settings are stored in a `.vscode` folder:
- `tasks.json`: Task Runner
- `launch.json`: Debugger

<!-- TODO: Launch Settings -->
<!--
"launch": {
    "configurations": [],
    "compounds": []
},
-->

Extensions are stored in `%USERPROFILE%\.vscode\extensions`.

I've got my own dotfiles that include settings but VSCode got you covered with a plugin
that saves it as a [Github GIST (example)](https://gist.github.com/Laoujin/eb201494af87c818a3756206352d69ba).
{% include github-stars.html url="shanalikhan/code-settings-sync" desc="Synchronize your Settings using Github GIST" %}



## Making it your own

{% include kbd k="Control+Shift+P" l="Show All Commands" %}

- **Preferences: Open Settings (UI)**: A pretty UI for common settings (Control ,)
- **Preferences: Open Settings (JSON)**: See all default settings and all your overrides (with intellisense!)
- **Preferences: Open Keyboard Shortcuts**: Searchable list with very convenient UI to assign shortcuts (Control K, Control S)
- **Preferences: Open Keyboard Shortcuts (JSON)**: See all possible shortcuts (with comments) and your overrides
  - [Official Docs: Keybindings](https://code.visualstudio.com/docs/getstarted/keybindings): Solid article, gets to advanced customization pretty rapidly


#### Settings.json

```javascript
{
    // Your settings
    "files.encoding": "utf8",
    "[ahk]": {
        // Your overrides for Authotkey
        "files.encoding": "utf8bom"
    },
}
```




# Starting VSCode

List of the [Core CLI options](https://code.visualstudio.com/docs/editor/command-line#_core-cli-options)
- `-n` or `--new-window`: Opens new instance instead of restoring previous session
- `--add <dir>`: Add folder(s) to the last active window for a multi-root workspace
- `-d` or `--diff`: Open diff view with two file paths as arguments
- `-g file:line:column`: Open file optionally at specific line and column
- `--install-extension`: Also uninstall, disable and list. [Whole list](https://code.visualstudio.com/docs/editor/command-line#_working-with-extensions).


#### Start from PowerShell

```powershell
$ide = "C:\Users\$($env:username)\AppData\Local\Programs\Microsoft VS Code\bin\Code.cmd"
function Start-VSCode {
	if ($args.length -eq 0 -or $null -eq $args[0]) {
		& $ide --new-window
		return
	}

	$folder = Resolve-Path $args
	& "$ide" --new-window "$folder"
}
Set-Alias cde Start-VSCode
```

#### Start from Windows Explorer Context Menu

Available during installation but [here is the reg file](/assets/blog-assets/vscode-contextmenu.reg)
if you want to add it later on (check the path inside the reg).


#### Start from VSCode

{% include kbd k="Control+K,O" l="Open active file in new instance" %}
{% include kbd k="Control+K,R" l="Reveal active file in Windows explorer" %}
{% include kbd k="Control+K,P" l="Copy path of active file" %}


#### Start from git

```ini
# ~/.gitconfig
[core]
    editor = code --wait

[merge]
	tool = vsc
[mergetool "vsc"]
	cmd = code --wait $MERGED

[diff]
    tool = vsc
[difftool "vsc"]
    cmd = code --wait --diff $LOCAL $REMOTE
```



# Layout

![alt](/assets/blog-images/vscode-window-layout.png){: .img-responsive}

{% include kbd k="F11" l="View: Toggle Full Screen" %}
{% include kbd k="Control+Q" l="View: Quick Open View" %}

<br>
{% include kbd k="Control+K,Z" l="View: Toggle Zen Mode (2xESC to exit)" %}

Some Zen Mode settings:
```javascript
// settings.json
{
    "zenMode.hideLineNumbers": false,
    "zenMode.hideActivityBar": false,
    "zenMode.hideStatusBar": false,
    "zenMode.hideTabs": true,
}
```

## Activity Bar

{% include kbd k="Control+Shift+E" l="Explorer" %}
{% include kbd k="Control+Shift+G" l="Source Control" %}
{% include kbd k="Control+Shift+D" l="Debug" %}
{% include kbd k="Control+Shift+X" l="Extensions" %}

<br>
Custom shortcut:
- `Control K, Control A`: View: Toggle Activity Bar Visibility

## Side Bar

{% include kbd k="Control+B" l="View: Toggle Side Bar Visibility" %}
{% include kbd k="Control+à" l="View: Focus into Side Bar" oem="0" %}

```javascript
{
    // By default VSCode tracks the current file in the Explorer.
    "explorer.autoReveal": false,

    // Drag & Drop
    "explorer.enableDragAndDrop": true,
    "explorer.confirmDragAndDrop": false,
    "explorer.confirmDelete": false,

    // Hide from Explorer
    "files.exclude": {
        "node_modules/**": true,
        ".sass-cache": true,
    },
}
```

Custom shortcuts:
- `Shift Alt L`: File: Reveal Active File in Side Bar
- `Control K, Control N`: View: Next Side Bar View
- `Control K, Control B`: View: Previous Side Bar View
- `Control E, Control F`: File: Collapse Folders in Explorer



## Panels

{% include kbd k="Control+J" l="View: Toggle Panel" %}
{% include kbd k="Control+Shift+Y" l="View: Debug Console" %}
{% include kbd k="Control+ù" l="View: Toggle Integrated Terminal" oem="backtick" %}
{% include kbd k="Control+Shift+U" l="View: Toggle Output" %}

<br>
Custom shortcuts:
- Remapped: `Control Alt ù`: View: Toggle Integrated Terminal (Conflicted with Cmder)
- View: Next Panel View



# Search

{% include kbd k="Control+F" l="Find (Navigate with F3)" %}
{% include kbd k="Control+Shift+F" l="View: Show Search (Navigate with F4)" %}
{% include kbd k="Control+H" l="Replace" %}
{% include kbd k="Control+Shift+H" l="Search: Replace in Files" %}

<br>
Once searching:
- `Alt Enter`: Select all matches
- `Control Alt Enter`: Replace all matches
- `Control Shift 1`: Replace one
- `Alt C`: Toggle Case Sensitive
- `Alt R`: Toggle Regex
- `Alt W`: Toggle Full Word


Toggle files to include/exclude (`Control Shift J`)
- Use `/` slashes
- Separate paths with `,`
- Prefix with `!` to negate
- Globbing
  - `*`: Match x characters in path segment
  - `**`: Match 0..x path segments
  - `?`: Match one character
  - `{**/*.css,**/*.scss}`: Group conditions
  - `[0-9a-z]`: Match range of characters


#### settings.json

```javascript
{
    "search.exclude": {
        "**/node_modules": true,
        "**/dist": true,
    },
    "search.location": "panel", // or: sidebar
    "search.showLineNumbers": true,
    // Search case-insensitively if the pattern is all lowercase, otherwise, search case-sensitively.
    "search.smartCase": false,
    "search.useGlobalIgnoreFiles": true, // Use global .gitignore
    "search.usePCRE2": true, // Use the PCRE2 regex engine (as supported by JavaScript)
                             // Required for backreferences and lookarounds

    "editor.find.seedSearchStringFromSelection": false,
    "editor.find.addExtraSpaceOnTop": false,
}
```

Custom shortcuts:
- Search: Collapse All
- Search: Toggle Search View Position (panel / sidebar)




# Terminal

I'm still using [Cmder](https://cmder.net/) for all my CLI needs so I haven't really
configured the Terminal much.
There is [this](https://www.growingwiththeweb.com/2017/03/mastering-vscodes-terminal.html)
pretty blog post for more Terminal info.

{% include kbd k="Control+ù" l="View: Toggle Integrated Terminal" oem="backtick" %}
{% include kbd k="Control+Shift+ù" l="Terminal: Create New Integrated Terminal" oem="backtick" %}
{% include kbd k="Control+Shift+5" l="Terminal: Split Terminal" %}


#### Integrate Cmder into VSCode

```javascript
// settings.json
{
    "terminal.integrated.shell.windows": "cmd.exe",
    "terminal.integrated.shellArgs.windows": [
        "/K",
        "C:\\Cmder\\vendor\\init.bat"
    ],
}
```


# Tasks

Stored in `.vscode\tasks.json` in your workspace path.

`Control+Shift+P` > Tasks: Configure Task > Create tasks.json file from template

```javascript
{
    "tasks": [{
        "label": "Compile Markdown",
        "type": "shell",
        "command": "markdown-it sample.md -o sample.html",
        "group": "build"
    }]
}
```

There are templates for: .NET Core, MSBuild and Maven.  
And of course there are extensions for
[Gulp](https://github.com/nickdodd79/vscode-gulptasks)
, [Cake](https://github.com/cake-build/cake-vscode)
, [CMake](https://github.com/microsoft/vscode-cmake-tools)
, [etc](https://github.com/lkytal/quickTask)



# Extensions

The main reason for the migration was the [VSCode Marketplace](https://marketplace.visualstudio.com/vscode).
Sublime still can't handle the React Fragment `<></>` syntax. And this one
project I used Vue on, well... the VSCode plugins were just superb while ST3 failed me again.

Usually all you have to do is open a new file extension and VSCode will jump to the opportunity
to have you install yet a few more extensions 😃


{% include kbd k="Control+Shift+X" l="Search Extensions in Marketplace" %}

The builtin search functionality is pretty slick. You will usually be able to find what you want.
Type a `@` to get a list of filters on category, enabled/disabled, recommended, to sort etc.

For example, [Vetur](https://github.com/vuejs/vetur) only comes up at #7 when searching for "Vue" but
soars the skies when searching for `vue @sort:installs`.

Find your newly installed extension with `Control Shift P`.
All its settings now show up in your `defaultSettings.json`.
A ⚙ (Gears) appears in the Side Bar after installation below the ⭐ (Stars) of the installed extension.
Click to configure it :)

#### Themes

{% include kbd k="Control+K,Control+T" l="Preferences: Color Theme (Search: @category:themes)" %}

Also: `Preferences: File Icon Theme`



#### Some Projects

(that don't show up pretty instantaneously when opening a file)

{% include github-stars.html url="ritwickdey/vscode-live-server" desc="Launch a development local Server with live reload feature for static & dynamic pages." %}
{% include github-stars.html url="Huachao/vscode-restclient" desc="REST Client" %}


#### For The Web

{% include github-stars.html url="ChristianKohler/PathIntellisense" desc="Autocompletes filenames" %}
{% include github-stars.html url="ChristianKohler/NpmIntellisense" desc="Autocomplete npm modules in import statements" %}
{% include github-stars.html url="wix/vscode-import-cost" desc="Display the import size of the package you are importing inside the code editor" %}
{% include github-stars.html url="lannonbr/vscode-js-annotations" desc="Add parameter name annotations to function calls" %}
{% include github-stars.html url="britesnow/vscode-toggle-quotes" desc="Simple cycling quotes toggler" %}

<br>
{% include github-stars.html url="vincaslt/vscode-highlight-matching-tag" desc="Highlights matching opening or closing Html tags" %}
{% include github-stars.html url="zignd/html-css-class-completion" desc="CSS class name completion for the HTML class attribute based on the CSS files in your workspace " %}
{% include github-stars.html url="pranaygp/vscode-css-peek" desc="Peek at CSS definitions from a class or id tag in Html" %}
{% include github-stars.html url="leehooi/vs-color-picker" desc="Select any color on the screen" %}

And all linters, formatters etc you can think of...


#### Making things stand out more

{% include github-stars.html url="oderwat/vscode-indent-rainbow" desc="Show indentation with a faint rainbow colored background" %}
{% include github-stars.html url="aaron-bond/better-comments" desc="Make TODO's really shout out 😃" %}
{% include github-stars.html url="CoenraadS/Bracket-Pair-Colorizer-2" desc="Bracket Colorizer" %}
{% include github-stars.html url="johnpapa/vscode-peacock" desc="Subtly change the color of your Visual Studio Code workspace, ideal for multi VSCode instances." %}
{% include github-stars.html url="IBM-Cloud/vscode-log-output-colorizer" desc="Syntax colorization for both the output/debug/extensions panel and *.log files." %}

```javascript
{
    "workbench.tree.indent": 32,
    "workbench.editor.highlightModifiedTabs": true,
}
```


# Conclusion

Very glad I made the switch. It was quite the effort but I'm pretty content already.
Still need to configure linting, formatting, languages setup and also fix the occasional
kink in my setup but the overall experience is already way beyond Sublime Text.

Aside from being a pretty great IDE out of the box, it's the plugins that really make VSCode shine.
Not only the quality offered but also the ease of actually finding them in the marketplace and their
configuration afterwards.

**Final score**: 👍 👍 👍 🌈 🌈
{: style="text-align: center; margin-top: 32px; font-size: 23px;"}

<br>
Next up: [VSCode: Editor Configuration and Shortcuts]({{ '/blog/dev-setup/vscode-editor' | relative_url}})
<br>

* * * *

#### Promise to self

If I ever need to change again because of slowed down development
or a dying community, it will be for an editor that will (hopefully!) outlive them all,
you know, something like Vim.

![Sublime Text? Real Americans Use Vim](/assets/blog-images/vscode-use-vim.jpg)

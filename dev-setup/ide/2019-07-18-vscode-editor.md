---
layout: post
author: Wouter Van Schandevijl
title: "VSCode: Editor Settings and Shortcuts"
date: 2019-07-18 00:01:00 +0200
desc: >
  The VSCode Editor shortcuts I should memorize, somehow.
  (preferably this year)
image: /assets/blog-images/vscode-editor.png
bigimg:
  url: vscode-editor-big.png
  desc: "Photo by Adi Goldstein"
  origin: https://unsplash.com/photos/mDinBvq1Sfg
img:
  url: vscode-editor.png
  desc: "Pretty IDE and pretty code."
  origin: https://unsplash.com/photos/cvBBO4PzWPg
  title: "Photo by Markus Spiske"
categories: dev-setup
tags: [cheat-sheet,tutorial,pragmatic-tips]
series: vscode
interesting:
  - url: 
    desc: ""
toc:
  title: Visual Studio Code
  icon:
package-versions:
  - package: vscode
    version: 1.36.0
---

![VSCode Editor](/assets/blog-images/vscode-editor.png){: .webfeedsFeaturedVisual }


> Pragmatic Tip 22:
> The editor should be an extension of your hand;
> make sure your editor is configurable, extensible,
> and programmable.


<!--more-->


# Basic Editing

{% include kbd k="Alt+Up/Down" l="Move Line Up/Down" %}
{% include kbd k="Shift+Alt+Up/Down" l="Copy Line Down/Up" %}
{% include kbd k="Control+Shift+K" l="Delete Line" %}
{% include kbd k="Control+L" l="Select whole line (expandLineSelection)" %}
{% include kbd k="Shift+Alt+Left/Right" l="Shrink/Expand Selection" %}
{% include kbd k="Control+/" l="Toggle Line Comment" %}

<br>
Custom shortcuts:
- `Control Shift J`: Join Lines
- Change End Of Line Sequence
- Change File Encoding
- Convert Indentation to Spaces/Tabs
- Detect Indentation from Content
- Copy With Syntax Highlighting (Also see extension [octref/polacode](https://github.com/octref/polacode))
- Sort Lines Ascending/Descending
- Transform to Lowercase / Title Case / Uppercase
- View: Toggle Control Characters
- View: Toggle Render Whitespace


# Settings

```javascript
// settings.json
// There is (extensive) intellisense for each setting key and its valid values!
// Some Control Shift P actions:
// Preferences: Open Settings (JSON)
// Preferences: Open Default Settings (JSON)
{
    "files.encoding": "utf8",
    "files.autoGuessEncoding": false,
    "files.eol": "auto",

    // Fira Code: Monospaced font with programming ligatures
    // https://github.com/tonsky/FiraCode
    "editor.fontFamily": "Fira Code, Consolas, 'Courier New', monospace",
    "editor.fontLigatures": true,

    "editor.fontSize": 14,
    "editor.cursorStyle": "line",
    "editor.cursorBlinking": "blink",
    "editor.cursorWidth": 4,
    "editor.letterSpacing": 0,

    // Whitespace
    "editor.tabSize": 4,
    "editor.autoIndent": true,
    "editor.detectIndentation": true,
    "editor.insertSpaces": true, // Ignored when detectIndentation is true
    "files.insertFinalNewline": true,
    "files.trimFinalNewlines": false,
    "files.trimTrailingWhitespace": true,
    "[markdown]": {
        "files.trimTrailingWhitespace": false,
    },
    "editor.renderWhitespace": "all",

    // Look
    "breadcrumbs.enabled": false,
    "editor.minimap.enabled": false,
    "editor.wordWrap": "off",
    "editor.wordWrapColumn": 140, // when wordWrap is wordWrapColumn or bounded
    "editor.rulers": [120, 180],

    // Feel
    "editor.autoClosingBrackets": "always",
    "editor.autoClosingQuotes": "always",
    "editor.autoSurround": "languageDefined",

    "files.defaultLanguage": "",
    "files.associations": {
        "*.ini.php": "ini",
    },
}
```


# File Management

{% include kbd k="Control+K,S" l="File: Save All" %}
{% include kbd k="Control+K,Control+W" l="View: Close All Editors" %}
{% include kbd k="Control+K,F" l="File: Close Workspace" %}
{% include kbd k="Control+K,Control+O" l="File: Open Folder..." %}
{% include kbd k="Control+K,M" l="Change language mode" %}
{% include kbd k="Alt+Z" l="View: Toggle Word Wrap" %}


<br>
Custom shortcut:
- `Control K, Control M`: View: Toggle Minimap



# Navigation

{% include kbd k="Control+P" l="Quick Open" %}
{% include kbd k="Control+R" l="File: Open Recent..." %}
{% include kbd k="Control+K,Control+P" l="View: Show All Editors" %}

<br>
{% include kbd k="Control+T" l="Go to Symbol in Workspace..." %}
{% include kbd k="Control+G" l="Go to Line..." %}
{% include kbd k="Control+Shift+O" l="Go to Symbol in File..." %}
{% include kbd k="Alt+Left/Right" l="Go back / forward" %}
{% include kbd k="Control+K,Control+Q" l="Go to Last Edit Location" %}
{% include kbd k="Control+Shift+µ" l="Go to matching Bracket" oem="\\" %}


# Folding

{% include kbd k="Control+Shift+)" l="Fold region" oem="[" %}
{% include kbd k="Control+Shift+^" l="Unfold region" oem="]" %}
{% include kbd k="Control+K,Control+à" l="Fold All" oem="0" %}
{% include kbd k="Control+K,Control+J" l="Unfold All" %}
{% include kbd k="Control+K,Control+!" l="Fold all regions" oem="8" %}
{% include kbd k="Control+K,Control+ç" l="Unfold all regions" oem="9" %}


# Intellisense

{% include kbd k="Control+Space" l="Trigger Suggest" %}

{% include kbd k="F12" l="Go to Definition (Or: Control + Click)" %}
{% include kbd k="Alt+F12" l="Peek Definition" %}
{% include kbd k="Control+K,F12" l="Open Definition on the Side (Or: Control + Alt + Click)" %}

<br>
{% include kbd k="Control+F12" l="Go to Implementation" %}
{% include kbd k="Control+Shift+F12" l="Peek Implementation" %}

<br>
{% include kbd k="Shift+F12" l="Peek References" %}
{% include kbd k="Shift+Alt+F12" l="References: Find All References" %}


```javascript
{
    "editor.tabCompletion": "on",
    "editor.suggestSelection": "first",
    "editor.acceptSuggestionOnEnter": "off",
    "editor.quickSuggestions": {
        "other": true,
        "comments": false,
        "strings": false
    },
    "editor.parameterHints.cycle": true,
}
```

# Snippets

{% include kbd k="Control+Alt+J" l="Insert Snippet" %}

`Control+Shift+P`: "Preferences: Configure User Snippets"
- New Global Snippets file...: Contains comments explaining how to create one
- Create snippets for a specific language or project
- Configure Existing Snippets

```javascript
{
    // Snippet Variables:
    // $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders.
    "console.log": {
        "scope": "typescript,javascript,typescriptreact,javascriptreact",
        "prefix": "cl",
        "body": ["console.log('$0');"],
        "description": "console.log()"
    }
}
```


# Multi Cursor

{% include kbd k="Control+Alt+Up/Down" l="Add Cursor Above/Below" %}
{% include kbd k="Shift+Alt+I" l="Add Cursors to Line Ends" %}

<br>
{% include kbd k="Control+D" l="Add Selection To Next Find Match" %}
{% include kbd k="Control+U" l="Soft Undo (Undo last cursor operation)" %}
{% include kbd k="Control+K,Control+D" l="Move Last Selection To Next Find Match" %}
{% include kbd k="Control+Shift+L" l="Select All Occurrences of Find Match" %}


#### Mouse Multi Cursor

{% include kbd k="Alt+Click" l="Insert Cursor" %}
{% include kbd k="Shift+Alt+Drag" l="Column (box) selection" %}

```javascript
// settings.json
{
    "editor.multiCursorModifier": "alt",
}
```


# Refactor

{% include kbd k="F2" l="Rename File (Explorer) / Rename Symbol (Editor)" %}
{% include kbd k="Control+;" l="Quick Fix... (Extract selection to method/constant)" %}
{% include kbd k="Control+Shift+R" l="Refactor..." %}
{% include kbd k="Control+K,Control+I" l="Show Hover" %}
{% include kbd k="Control+²" l="Toggle Quote (Extension BriteSnow.vscode-toggle-quotes)" oem="'" %}

#### Problems

{% include kbd k="Control+Shift+M" l="View: Toggle Problems Panel (Errors, Warnings, ...)" %}
{% include kbd k="F8" l="Cycle through errors (Shift to reverse cycle)" %}




# Editor Groups

{: style="margin-bottom: 12px;"}
{% include kbd k="Control+Enter" l="In Side Bar Explorer: Open to the Side (Or: Alt + Click)" %}

{% include kbd k="Control+K,Arrow" l="View: Move Editor Group" %}
{% include kbd k="Control+K,Control+Left/Right" l="View: Focus Left/Right Editor Group" %}
{% include kbd k="Control+Alt+Left/Right" l="View: Move Editor into Prev/Next Group" %}
{% include kbd k="Control+PageUp/Down" l="View: Move Editor (Tab) Left/Right" %}
{% include kbd k="Control+Tab" l="View: Open Next Recently Used Editor" %}
{% include kbd k="Shift+Alt+à" l="View: Toggle Vertical/Horizontal Editor Layout" oem="0" %}
{% include kbd k="Control+1 / 2 / 3" l="View: Focus 1st / 2nd / 3rd Editor Group" %}


#### Closing them

{% include kbd k="Control+K,Control+W" l="View: Close All Editors" %}
{% include kbd k="Control+K,Control+Shift+W" l="View: Close All Editor Groups" %}


<br>
Custom shortcuts:
- View: Grid Editor Layout (2x2)
- View: Split Editor
- View: Two/Three Rows/Columns Editor Layout

```javascript
// settings.json
{
    "workbench.editor.showTabs": true,
    "workbench.editor.highlightModifiedTabs": true,
    "workbench.editor.labelFormat": "default",
    "workbench.editor.tabCloseButton": "off",
    "workbench.editor.tabSizing": "shrink",
}
```


# Formatting

{% include kbd k="Shift+Alt+F" l="Format Document" %}
{% include kbd k="Control+K,Control+F" l="Format Selection" %}
{% include kbd k="Shift+Alt+O" l="Organize Imports" %}

```javascript
// settings.json
{
    "editor.defaultFormatter": null,

    "editor.formatOnPaste": true,
    "editor.formatOnSaveTimeout": 750,

    "editor.formatOnSave": false,
    "editor.codeActionsOnSave": {},
    "editor.codeActionsOnSaveTimeout": 750,
}
```



# Markdown

I guess this is here because I've done mostly markdown - writing this :)

{% include kbd k="Control+K,V" l="Markdown: Open Preview to the Side" %}
{% include kbd k="Control+Shift+V" l="Markdown: Open Preview" %}

<br>
{% include github-stars.html url="yzhang-gh/vscode-markdown" desc="Add intuitive keyboard shortcuts, TOC, table formatter, autocompletion and more!" %}
{% include github-stars.html url="davidanson/vscode-markdownlint" desc="Markdown linting and style checking" %}

{% include github-stars.html url="streetsidesoftware/vscode-spell-checker" desc="Source code spell checker" %}

```javascript
{
    "markdown.preview.scrollPreviewWithEditor": true,
    "markdown.preview.scrollEditorWithPreview": true,

    // davidanson.vscode-markdownlint
    "markdownlint.config": {
        "default": true,

        // https://github.com/DavidAnson/vscode-markdownlint#rules
        "single-title": false,
    },

    // yzhang.vscode-markdown
    "markdown.extension.italic.indicator": "*", // or -
    "markdown.extension.list.indentationSize": "inherit",
    "markdown.extension.preview.autoShowPreviewToSide": false,
    "markdown.extension.toc.githubCompatibility": true,
}
```

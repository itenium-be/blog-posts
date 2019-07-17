---
layout: post
author: Wouter Van Schandevijl
title:  "Advanced Windows Explorer"
date:   2017-04-22 02:00:00 +0200
categories: productivity
img:
  url: explorer.gif
tags: [autohotkey,powershell,windows]
toc:
  title: Windows Explorer
  icon: windows
lastUpdate: 2018-08-30 19:23:30 +0200
updates:
  - date: 2018-08-30 19:23:30 +0200
    desc: Hijacking Windows + E
---

A listing of handy but less known shortcuts in Windows Explorer
and some Autohotkey examples on how to add extra functionality.

Open Windows Explorer, the most direct way:  
{: style="display: inline"}
{% include kbd k="Win+E" l="Open Windows Explorer" %}

<br>
<!--more-->

From a PowerShell prompt: `ii .` or `$aPath | ii`  
From a cmd prompt: `start .`

From within Windows Explorer itself:
{: style="display: inline"}
{% include kbd k="Control+N" l="Open new window in same path" %}



* * *



Builtin shortcuts
=================

{% include kbd k="Control+Shift+N" l="Create a new directory" %}
{% include kbd k="F2" l="Rename selected. Follow with Tab to rename next." %}
{% include kbd k="Shift+Delete" l="Delete permanently" %}
{% include kbd k="Control+C,Control+V" l="Duplicate file" %}



{% include kbd k="Shift+F10" l="Open context menu (or Menu) = Right click" %}
{% include kbd k="Alt+Enter" l="Open directory/file properties (or Alt+Double click)" %}
{% include kbd k="Control+Shift+6" l="Switch to details view" %}
The 6 can be replaced with any number from 1 (Extra large icons) to 4 (Small icons),
5 (List), 7 (Tiles) and 8 (Content).


{% include kbd k="Control+L" l="Focus AddressBar (or Alt+D)" %}
{% include kbd k="F4" l="Open AddressBar dropdown with last typed locations" %}
The locations are stored in: HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths



**Navigation**:  
{% include kbd k="Alt+Up" l="Go to parent folder" %}
{% include kbd k="Alt+Left" l="Go to previous folder (or Backspace)" %}
{% include kbd k="Alt+Right" l="Go to next folder" %}

<br>
**Drag &amp; Drop**:  
{% include kbd k="Control" l="Copy dragged file(s)" %}
{% include kbd k="Shift" l="Move dragged file(s)" %}
{% include kbd k="Control+Shift" l="Create shortcut" %}

## Create a new dotfile

To work around "You must type a file name", append an additional dot at the end of the filename.
```
.gitignore.
```
Or in PowerShell: `ac .gitignore "node_modules"`



## Back to the CLI

Alt+D followed by cmd or powershell will open a prompt at the current path.
{: style="display: inline"}
{% include kbd k="Shift+F10,w" l="Open command prompt in selected folder" %}

<br>

## Jump to an env var


Alt+D followed by `%APPDATA%`, `%USERPROFILE%` or any environment variable.  
Or one of the shell commands like `shell:startup`, `shell:sendto` or `shell:desktop`

```powershell
# List all "shell:" locations
# (Not nearly all paths are printed because of naming mismatches)
$path = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions"
Get-ChildItem $path | Select-Object `
	@{l="Name";e={$_.GetValue("Name")}},`
	@{l="Path";e={[Environment]::GetFolderPath($_.GetValue("Name")).Replace(" ", "")}}
```

## Less used shortcuts

{% include kbd k="Control+F1" l="Toggle Ribbon" %}
{% include kbd k="Control+Mousewheel" l="Scroll through the different views" %}
{% include kbd k="Alt+P" l="Toggle preview pane" %}
{% include kbd k="Control+F" l="Focus the search box" %}
{% include kbd k="F11" l="Full screen" %}
{% include kbd k="F5" l="Refresh" %}
{% include kbd k="Right" l="Select first subfolder when there is no selection" %}



Autohotkey Extensions
=====================

Add missing stuff yourself with Autohotkey.  
Find [the source for the Autohotkey scripts here][github-ahk]

Execute shortcut code only when Windows Explorer is active:
```autohotkey
SetTitleMatchMode Regex
#IfWinActive ahk_class CabinetWClass|ExploreWClass|Progman|WorkerW
; "ahk_class CabinetWClass|ExploreWClass" executes in Windows Explorer
; "ahk_class Progman|WorkerW" executes on Desktop
#IfWinActive
```

**Some of the scripts use the following methods.
They are defined in [windows-explorer-util.ahk][github-ahk-utility].**

```autohotkey
; Get the currently active path
Explorer_GetPath()

; Get the currently selected file(s)
Explorer_GetSelected()
```

## Hijacking Windows + E

If the selected text while pressing Windows E is a path,
open Windows Explorer in that specific path.

```autohotkey
~#e::
; Save clipboard
oldClip := clipboard
; Send Control+C
Send, ^c
ClipWait, 3
clipVal := clipboard

; Does the copied text contain a path?
fileExists := FileExist(clipVal)
if (fileExists = "D") {
    ; Open explorer in specific directory
    Run % "explorer.exe /root," clipVal

} else if (fileExists) {
    ; Open explorer with file selected
    Run % "explorer.exe /select," clipVal

} else {
    ; Simply open explorer
    Send #e
}

; Restore the clipboard
clipboard := oldClip
Return
```


## Zip directory

[Ahk source][github-ahk-zip] for creating a zip file with the contents of
the current directory or the currently selected files.


## Create new file

One creates a new directory with Control + Shift + N but there is not really one for
creating a new file.

```autohotkey
; Control + Shift + F: New file
^+f::
DeselectSelectedFiles()
Send {Appskey}
Send w
Send t
Send ^a
Return

; Control + Shift + T: New txt file
^+t::
DeselectSelectedFiles()
Send {Appskey}
Send w
Send t
Return

DeselectSelectedFiles()
{
	; Explorer_GetSelected is defined in utilities/windows-explorer-util.ahk
	selectedFiles := Explorer_GetSelected()
	if selectedFiles
	{
		; If no file is selected, ^Space may actually select it
		; after which {AppsKey} will open the wrong ContextMenu
		Send ^{Space}
	}
}
```

## Other

**Change extension dialog**:  
Windows doesn't offer an option to not show a confirmation dialog
when changing the extension of a file. The best we can do is wait
and whenever one pops up click it away?

```autohotkey
While, 1
{
    WinWaitActive, Rename ahk_class #32770
    Send y
}
```


**Details View**:  
While `Control + Shift + 6` is probably very useful, pesky Windows
might truncate longer filenames with the switch to Details View.

```autohotkey
; Control + Shift + 6: Details View
; ~ = Do not block native function
~^+6::
; Resize columns so that filenames are completely visible
Send {Control Down}{NumpadAdd}{Control Up}
Return
```


**Closing**:  
Closing an Explorer window can be done with Alt+F4 or with Control+W.
But when you want to close it after Windows annoyed you, this scrips allows you
to you to do so by hitting Esc hard, twice.

```autohotkey
~Esc::
If (A_PriorHotKey = A_ThisHotKey and A_TimeSincePriorHotkey < 500)
	WinClose

Return
```
(I guess I just didn't know about Control + W before this post:)


* * *


**Alternatives**:  

According to [AlternativeTo Windows Explorer][alternatives], many people like Total Commander.



[github-ahk]: https://github.com/itenium-be/Mi-Ke/tree/master/scripts-windows-explorer
[github-ahk-zip]: https://github.com/itenium-be/Mi-Ke/blob/master/scripts-windows-explorer/windows-explorer-zip-directory.ahk
[github-ahk-utility]: https://github.com/itenium-be/Mi-Ke/blob/master/scripts-windows-explorer/windows-explorer-util.ahk
[alternatives]: http://alternativeto.net/software/windows-explorer

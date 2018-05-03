---
layout: post
author: Wouter Van Schandevijl
title:  "Autohotkey Tutorial"
date:   2018-05-01 00:00:00 +0200
categories: productivity
tags: [autohotkey]
---

Perils of Autohotkey
- Lost forum downloads
- Branches


Do a post on debugging:
https://autohotkey.com/docs/FAQ.htm#Debug
https://autohotkey.com/docs/Scripts.htm#debug


https://github.com/Pulover/PuloversMacroCreator


Context sensitive hotkeys
--> This part also applies hotstrings
--> And probably deserves its own part in the series
https://autohotkey.com/docs/commands/_IfWinActive.htm
https://autohotkey.com/docs/commands/_If.htm
https://autohotkey.com/docs/misc/WinTitle.htm
https://autohotkey.com/docs/commands/SetTitleMatchMode.htm
--> Then refer to it in the autohotkey-hotkeys part..
https://autohotkey.com/docs/commands/WinWait.htm


https://autohotkey.com/docs/Hotstrings.htm
#Hotstring c r


; AltGr+Win: Go directly to special folders
; +D: Dropbox
; +W: WWW Folder
; +T: Temp folder
; +L: Leeching folder
<^>!#d::Run C:\Dropbox\Personal
<^>!#w::Run C:\wamp\www
<^>!#t::Run C:\temp
<^>!#l::Run C:\Download


There is no one way to do things in Autohotkey.
It's syntax is pretty loose which might be confusing when starting out
and all example codes seems to be written differently.

Variable assignment
x = 5
x := 5


Autohotkey Hotstrings
=====================
http://www.autohotkey.com/docs/Hotstrings.htm
http://irisclasson.com/2015/02/19/autohotkey-hotstrings-and-sending-input/

; Default end characters:
#Hotstring EndChars -()[]{}:;'"/\,.?!`n `t
; or use * to trigger with any keypress


```
::btw::by the way
```


<!--more-->

**Versions**:  
Oh boy :)
AutoHotkey_L
-> Much worse than the penicle Python finds itself in.


**Running**:  
Put the ahk file in the startup folder
```
[Environment]::GetFolderPath("Startup")
```

The installer comes with
- `Ahk2Exe.exe` to compile ahk to exe
- An Active Window Spy GUI to find Window ahk_ values



` Escape character


Clipboard
---------


```ahk
; how long to keep trying to access the clipboard
; when the first attempt fails
#ClipboardTimeout 2000

content = %Clipboard%
ClipWait [secondsToWait]
Clipboard = New Text

OnClipboardChange:
	MsgBox test
return

#Persistent
OnClipboardChange("ClipChanged")
return

ClipChanged(Type) {
    ToolTip Clipboard data type: %Type%
    Sleep 1000
    ToolTip  ; Turn off the tip.
}

; Append, Ctrl+Shift+C, adds newline plus selected text to clipboard.
^+c::
   bak = %clipboard%
   Send, ^c
   clipboard = %bak%`r`n%clipboard%
return


; Replace, Ctrl+Shift+V, swaps selected text with clipboard text.
^+v::
   itemOne = %clipboard%
   Send, ^c
   itemTwo = %clipboard%
   clipboard = %itemOne%
   Send, ^v
   clipboard = %itemTwo%
return
```



Three options:  

Scripts 
Custom hotkeys 
and hotstrings




https://github.com/maul-esel/ahkbook
http://lifehacker.com/316589/turn-any-action-into-a-keyboard-shortcut
howtogeek.com




Difference between Autohotkey &amp; Autohotkey_L


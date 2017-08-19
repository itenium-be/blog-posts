---
layout: post
title:  "Auothotkey Tutorial"
date:   2017-04-22 02:00:00 +0200
categories: productivity
tags: [autohotkey]
---

There is no one way to do things in Autohotkey.
It's syntax is pretty loose which might be confusing when starting out
and all example codes seems to be written differently.

Variable assignment
x = 5
x := 5


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

--> Clipboard separate article for Clipboard stuff with compare tool added

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


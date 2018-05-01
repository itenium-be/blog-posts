---
layout: post
author: Wouter Van Schandevijl
title:  "Autohotkey Tutorial - hotkeys"
desc: >
    Autohotkey allows you to define hotkeys like ^#D.
    But what does it mean?
img: autohotkey.png
date:   2018-05-01 00:00:00 +0200
categories: productivity
tags: [autohotkey]
series: autohotkey
extras:
  - githubproject: 
toc:
    title: "Autohotkey series: Hotkeys"
    icon: car
---

First we'll cover the basic Autohotkey syntax.

After adding your first x Autohotkey hotkeys, it will become harder and harder
to think of new key combinations that are somehow still memorable.
--> TODO: We'll probably better split this in two posts?

We'll cover
- The basic hotkey bindings
- Some gotchas within those basics
- And some creative hotkey combinations


# Example
{: .hide-from-excerpt}

```autohotkey
; Single line hotkey
^#D::MsgBox Pressed Control + Win + D

; Multi line script
^#D::
MsgBox Multiball
Return
```

<!--more-->

# Syntax


## Keyboard

| Shorthand | Full            | Desc
|-----------|-----------------|----------
| +         | Shift
| ^         | Control or Ctrl |
| #         | Win             | Windows
| <#        | LWin            | Left Windows
| #>        | RWin            | Right Windows
| !         | Alt
| <^>!      | AltGr
| AppsKey   | 
|-----------|-----------------|
{: .table-code}

Others:  

- F[1-24], Tab, CapsLock, Space, AppsKey, Enter/Return, Backspace/BS
- Left, Up, Down, Right
- Esc, Ins, Delete/Del, Home, End, PgUp, PgDn
- PrintScreen, ScrollLock, CtrlBreak


Numpad:  

- Numlock ON: Numpad[0-9],
- Numlock OFF: NumpadIns, NumpadEnd, NumpadMult, NumpadEnter, ...


Multimedia: Browser_Stop, Media_Next, Volume_Up, Launch_App2, ...



## Mouse

- LButton, RButton, MButton
- XButton1 (Browser_Back), XButton2 (Browser_Forward)
- WheelUp, WheelDown, WheelLeft, WheelRight

Joystick: JoyX, JoyPOV, JoyInfo, ...



## Native function

Prefix a hotkey with `~` to not block the native function.  
```ahk
; Control + Shift + 6: Details View in Windows Explorer
; Resize columns so that filenames are completely visible
~^+6::Send {Control Down}{NumpadAdd}{Control Up}
```


## Multiple keys pressed together

Search google for text on the clipboard by pressing Capslock and X.
```ahk
Capslock & X::Run, http://www.google.com/search?q=%clipboard%
```


# Scripting

Creating more advanced hotkey combinations.


## Context sensitive shortcuts

```ahk
#IfWinActive, ahk_class Notepad
^a::MsgBox You pressed Ctrl-A while Notepad is active. Pressing Ctrl-A in any other window will pass the Ctrl-A keystroke to that window.
#c::MsgBox You pressed Win-C while Notepad is active.

#IfWinActive
#c::MsgBox You pressed Win-C while any window except Notepad is active.

#If MouseIsOver("ahk_class Shell_TrayWnd")
WheelUp::Send {Volume_Up}     ; Wheel over taskbar: increase/decrease volume.
WheelDown::Send {Volume_Down} ;
```



## Combine keypresses

Esc twice to close an application
```ahk
Esc::
    If (A_PriorHotKey = A_ThisHotKey and A_TimeSincePriorHotkey < 500)
        WinClose
    Else
        Send, {Esc}
Return
```



TODO: we zaten hier:

--> Autohotkey: HeidiSql doesn't open in foreground anymore

TODO: also example with different Prior / Next hotkey
like Control + T, Control + W

https://stackoverflow.com/questions/1794258/detect-a-double-key-press-in-autohotkey
https://autohotkey.com/docs/commands/KeyWait.htm#Examples
https://autohotkey.com/docs/commands/SetTimer.htm#Examples

https://autohotkey.com/board/topic/84052-a-priorhotkey-command/

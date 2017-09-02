---
layout: post
title:  "Autohotkey Tutorial - defining hotkeys"
desc: >
    Autohotkey allows you to define hotkeys like ^#D.
    But what does it mean?
img: autohotkey.png
date:   2017-09-02 00:00:00 +0200
categories: productivity
tags: [autohotkey]
---

# Example

```ahk
; Single line hotkey
^#D::MsgBox Pressed Control + Win + D

; Multi line script
^#D::
MsgBox Multiball
Return
```

# Syntax


## Keyboard

| Shorthand | Full            | Desc
|-----------|-----------------|----------
| ^         | Control or Ctrl |
| #         | Win             | Windows
| +         | Shift
| !         | Alt
| <#        | LWin            | Left Windows
| #>        | RWin            | Right Windows
| <^>!      | AltGr
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

<!--more-->


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


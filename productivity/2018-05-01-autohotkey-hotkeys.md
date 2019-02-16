---
layout: post
author: Wouter Van Schandevijl
title:  "Autohotkey Tutorial - hotkeys"
desc: >
  Autohotkey allows you to define hotkeys like ^#D.
  But what does it mean?
img: hotkeys.jpg
date:   2018-05-03 00:00:00 +0200
categories: productivity
tags: [autohotkey]
series: autohotkey
extras:
  - githubproject: https://github.com/itenium-be/Mi-Ke
toc:
  title: "Autohotkey series: Hotkeys"
  icon: car
interesting:
  - desc: Official Hotkeys documentation
    url: https://www.autohotkey.com/docs/Hotkeys.htm
  - desc: List of Keys (Keyboard, Mouse and Joystick)
    url: https://autohotkey.com/docs/KeyList.htm
  - desc: Hold down a key to send a different key
    git: babin101/DevKeyboard
  - desc: Morse code by Laszlo
    url: https://autohotkey.com/board/topic/15574-morse-find-hotkey-press-and-hold-patterns/
lastUpdate: 2018-08-25 00:56:00 +0200
updates:
  - date: 2018-08-25 00:56:00 +0200
---

First we'll cover the basic Autohotkey hotkey syntax. Which is arguably already pretty confusing for
newcomers in and by itself.  
<br>
But we don't stop there as after adding more and more hotkeys, it will also become harder and harder
to think of new key combinations that are somehow still memorable. There is only so much
you can do with the `#` (Windows) key etc.  
<br>
Time for some creative hotkey combinations! 'Advanced Hotkeys' covers code snippets on
how to run different scripts on single, double or triple key presses as well as how to
differentiate between long(ish) key presses or mouse clicks. And more...

# Example
{: .hide-from-excerpt}

```autohotkey
; Single line hotkey
^#D::MsgBox Pressed Control + Win + D (%A_ThisHotKey%)

; Control + Win + C: Multi line script
^#C::
Run, notepad.exe
WinWait, Untitled - Notepad, , 3
Send, Dear sir,{enter}{enter}
Return
```

<!--more-->

# Basic Syntax


## Keyboard

| Shorthand | Full            | Desc
|-----------|-----------------|----------
| +         | Shift
| ^         | Control or Ctrl |
| #         | Win             | Windows
| !         | Alt
| <#        | LWin            | Left Windows
| +>        | RShift          | Right Shift
| <^>!      | AltGr
|           | AppsKey         | Menu key
|           | CapsLock
|-----------|-----------------|
{: .table-code}

**Others**:  

- F[1-24], Tab, Space, Enter/Return, Backspace/BS
- Left, Up, Down, Right
- Esc, Ins, Delete/Del, Home, End, PgUp, PgDn
- PrintScreen, ScrollLock, CtrlBreak
- Sleep, Pause

<br>
**Numpad**:  

- Numlock ON: Numpad[0-9],
- Numlock OFF: NumpadIns, NumpadEnd, NumpadMult, NumpadEnter, ...

<br>
**Multimedia**:  

- Browser_Stop, Media_Next, Volume_Up, Launch_App2, ...

<br>
**Mouse**:  

- LButton, RButton, MButton
- XButton1 (Browser_Back), XButton2 (Browser_Forward)
- WheelUp, WheelDown, WheelLeft, WheelRight

<br>
**Joystick**:  

- JoyX, JoyPOV, JoyInfo, ...

[JoystickTest](https://autohotkey.com/docs/scripts/JoystickTest.htm): A simple script 
for determining the exact mapping of your joystick.

<br>
Find [the full KeyList here](https://autohotkey.com/docs/KeyList.htm).
If your keyboard has keys that are not in the full keylist,
[it might still be possible to map them to a script](https://autohotkey.com/docs/KeyList.htm#SpecialKeys).

You can also let Autohotkey itself do the figuring out for you. Its builtin command
`KeyHistory` opens a window where you can figure out which is which.

## Native function

Prefix a hotkey with `~` to not block the native function.  

```ahk
#IfWinActive, ahk_class CabinetWClass
; Control + Shift + 6: Details View in Windows Explorer
; Resize columns so that filenames are completely visible
~^+6::Send, {Control Down}{NumpadAdd}{Control Up}
#IfWinActive
```


## Custom combinations

To combine keys that do not have a shorthand use `&`.
Keys on the left side of such custom combinations lose their native function.
They are called prefix keys.


Example: Search google for text on the clipboard by pressing Capslock and X.
```ahk
Capslock & X::Run, http://www.google.com/search?q=%clipboard%
```


## Other

Add `UP` to a hotkey combination to run the script on key release rather than on keypress.  

```ahk
AppsKey::ToolTip Press < or > to cycle through tabs.
AppsKey Up::ToolTip
~AppsKey & <::Send ^+{tab}
~AppsKey & >::Send ^{tab}
```


<br>
`*` acts as a key modifier wildcard.

```ahk
*#c::Msgbox Triggered by Win+C, Shift+Win+C, Ctrl+Win+C, etc
```


<br>
`$` is necessary when you need to send the same hotkey and not execute your script again.

```ahk
$#e::
; Send Control + C => Copy text
Send, ^c
ClipWait, 1
fileExists := FileExist(clipboard)
if (fileExists = "D") {
    ; Open explorer in path on clipboard
    Run % "explorer.exe /root," clipboard

} else if (fileExists) {
    ; Open explorer with file selected
    Run % "explorer.exe /select," clipboard

} else {
    ; No path on clipboard, just open explorer
    Send #e
}
Return
```


# Advanced Hotkeys

Creating more advanced hotkey combinations.


## Context sensitive hotkeys

Control in which applications a hotkey is (not) active with the
keywords 
[`IfWinActive`](https://autohotkey.com/docs/commands/WinActive.htm), 
`IfWinExist`, `IfWinNotActive` and `IfWinNotExist`.


```ahk
#IfWinActive, ahk_class Notepad
^a::MsgBox You pressed Ctrl-A while Notepad is active. Pressing Ctrl-A in any other window will pass the Ctrl-A keystroke to that window.
#c::MsgBox You pressed Win-C while Notepad is active.
#IfWinActive

#c::MsgBox You pressed Win-C while any window except Notepad is active.


#If MouseIsOver("ahk_class Shell_TrayWnd") and true
WheelUp::Send {Volume_Up}     ; Wheel over taskbar: increase/decrease volume.
WheelDown::Send {Volume_Down} ;

MouseIsOver(WinTitle) {
    MouseGetPos,,, Win
    return WinExist(WinTitle . " ahk_id " . Win)
}
#If


$^p::
IfWinActive ahk_class Notepad
    return  ; i.e. do nothing, which causes Control-P to do nothing in Notepad.

Send ^p
return
```

Find out the `ahk_class` of a program with Window Spy (AU3_Spy.exe)
which comes bundled with Autohotkey itself.


## Combining 2 hotkeys

**Check if a hotkey combination is pressed twice**  

```ahk
~Esc::
; Esc twice to close an application
If (A_PriorHotKey = A_ThisHotKey and A_TimeSincePriorHotkey < 500)
    WinClose
Return
```

Gotcha: the `A_PriorHotkey` will only hold values of combinations that are also defined
as a hotkey. The value of `A_PriorHotkey` will be exactly as the hotkey is defined.


<br>
**Follow a hotkey up with an additional keypress**  

```ahk
; AltGr + C: Change the case of the selected text
; Follow up with (U)PPER, l(ower) or T(itle)
<^>!c::
clipboard =
Send, ^c
ClipWait, 1
Input keyPressed, I L1
if (keyPressed = "u") {
    StringUpper toUpperCase, clipboard
    Send %toUpperCase%
} else if (keyPressed = "l") {
    StringLower toLowerCase, clipboard
    Send %toLowerCase%
} else if (keyPressed = "p" or keyPressed = "t") {
    StringUpper toTitleCase, clipboard, T
    Send %toTitleCase%
}
return
```

[`Input`](https://autohotkey.com/docs/commands/Input.htm) option `L1` is the maximum allowed length. (L1 -> L5)

## Double, triple and long key presses

```ahk
#c::
DoubleClickTime := DllCall("GetDoubleClickTime") ; in milliseconds

; Wait for 'c' to be released
KeyWait, c
if (A_TimeSinceThisHotkey > DoubleClickTime) {
    Msgbox Longpress
    return
}

; Wait for 'c' to be pressed down again (option "d")
; But timeout after T0.5 seconds (If DoubleClickTime is 500)
KeyWait, c, % "d T"DoubleClickTime/1000
If ! Errorlevel
    Msgbox Doublepress
else
    Msgbox Singlepress
Return
```

[`KeyWait`](https://autohotkey.com/docs/commands/KeyWait.htm) docs
or [more info on the forum](https://autohotkey.com/board/topic/51354-singlepress-doublepress-and-pressandhold/).

<br>
**Long middle mouse click with [`GetKeyState`](https://autohotkey.com/docs/commands/GetKeyState.htm)**  

```ahk
~MButton::
HowLong = 0
Loop
{
    HowLong++
    Sleep, 10
    GetKeyState, MButton, MButton, P
    IfEqual, MButton, U, Break
}
IfLess, HowLong, 20, Return

; Do stuff when middle mouse button was pressed longer than 20ms
```

<br>
**Triple, quadruple etc keypresses**  

```autohotkey
; Example #3: Detection of single, double, and triple-presses of a hotkey. This
; allows a hotkey to perform a different operation depending on how many times
; you press it:
#j::
if winc_presses > 0 ; SetTimer already started, so we log the keypress instead.
{
    winc_presses += 1
    return
}
; Otherwise, this is the first press of a new series. Set count to 1 and start
; the timer:
winc_presses = 1
SetTimer, KeyWinC, -400 ; Wait for more presses within a 400 millisecond window.
return

KeyWinC:
if winc_presses = 1 ; The key was pressed once.
{
    Run, c:\  ; Open a folder.
}
else if winc_presses = 2 ; The key was pressed twice.
{
    Run, c:\windows  ; Open a different folder.
}
else if winc_presses > 2
{
    MsgBox, Three or more clicks detected.
}
; Regardless of which action above was triggered, reset the count to
; prepare for the next series of presses:
winc_presses = 0
return
```


## Morse hotkeys

The [forum thread on hold patterns](https://autohotkey.com/board/topic/15574-morse-find-hotkey-press-and-hold-patterns).

```ahk
Morse(timeout = 400) {
   tout := timeout/1000
   key := RegExReplace(A_ThisHotKey,"[\*\~\$\#\+\!\^]")
   Loop {
      t := A_TickCount
      KeyWait %key%
      Pattern .= A_TickCount-t > timeout
      KeyWait %key%,DT%tout%
      If (ErrorLevel)
         Return Pattern
   }
}

; Use Backspace
BS::MsgBox % "Morse press pattern " Morse()

; Alt + Z
!z::
p := Morse()
If (p = "0")
  MsgBox Short press
Else If (p = "00")
  MsgBox Two short presses
Else If (p = "01")
  MsgBox Short+Long press
Else
  MsgBox Press pattern %p%
Return
```
# Other

## Waiting for something

Wait for something to happen with [`SetTimer`](https://autohotkey.com/docs/commands/SetTimer.htm)

```ahk
; Example #2: Wait for a certain window to appear and then alert the user:
#Persistent
SetTimer, Alert1, 500
return

Alert1:
IfWinNotExist, Video Conversion, Process Complete
    return

; Otherwise:
SetTimer, Alert1, Off  ; i.e. the timer turns itself off here.
SplashTextOn, , , The video conversion is finished.
Sleep, 3000
SplashTextOff
return
```


## Suspension

```autohotkey
; Toggle script suspension
^#s:: ; Control + Win + S
Suspend
Msgbox % "Script is now " (A_IsSuspended ? "suspended" : "running")
Return
```

The [`Pause`](https://autohotkey.com/docs/commands/Pause.htm) keyword would halt the current thread 
while [`Suspend`](https://autohotkey.com/docs/commands/Suspend.htm) stops all hotkeys for triggering.

Start a script with `Suspend, Permit` to have that hotkey still trigger
even while the script is suspended.

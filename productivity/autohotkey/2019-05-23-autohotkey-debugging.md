---
layout: post
author: Wouter Van Schandevijl
title:  "Autohotkey Debugging"
date:   2019-05-23
desc: >
  How to speed up and perhaps also help you get out of a pinch
  while debugging your Authotkey scripts...
bigimg: 
  url: autohotkey-development-debugging-big.png
  desc: "Photo by Jason D"
  origin: https://unsplash.com/photos/7BhTfoKsheQ
img:
  url: autohotkey-development-debugging.png
  desc: "Is that even Authotkey code?? Are you kidding me???"
  origin: https://unsplash.com/photos/5VM5SHrs_E8
  title: "Photo by Taras Shypka"
  linkUrl: 
categories: productivity
tags: [autohotkey,testing,debugging]
extras:
interesting:
  - git: ahkscript/awesome-AutoHotkey
    desc: "A curated list of awesome"
  - git: itenium-be/Mi-Ke
    desc: "Mi-Ke contains most stuff in this blog post, and then some."
toc:
  title: 🐛 To The Trenches
  icon: 
todo:
  - Setting the default editor for "Edit This Script" tray menu item
  - Interactive debugging?
---

If you are like me, you just started writing Authotkey scripts without ever
really taking the time to learn the language properly. This blog post contains some
scripts and tools to help you try make some sense of what is going on.

<!--more-->

# Yunit

{% include github-stars.html url="Uberi/Yunit" desc="Nothing beats a test runner: Uberi and infogulch created one." %}

Our fork of the super simple Yunit Autohotkey testing framework
adds a ps1 colored console runner and monitoring. 
Check our [Github README](https://github.com/itenium-be/Yunit) for a screenshot and code example.


# Builtin functionality

## MsgBox

MsgBox pauses the current thread so you can use "other debugging tools"
while displaying something.

```autohotkey
var := 10
vas := 5
MsgBox aaargh %var%
MsgBox % var "and" vas
```

#### Tooltip

[`Tooltip`](https://autohotkey.com/docs/commands/ToolTip.htm) 
creates a non-blocking, always-on-top window anywhere on the screen.

```autohotkey
Tooltip [, Text`nMoreText, X, Y, WhichTooltip]
```

- If Text is omitted, the tooltip is hidden.
- If X & Y are omitted, the tooltip is displayed near the mouse cursor.
- X & Y are relative to the active window. Use [`CoordMode`](https://autohotkey.com/docs/commands/CoordMode.htm) to display somewhere else.
- WhichTooltip: A number between 1-20 to control multiple tooltips.


## Windows

I'll be honest, they are not the prettiest tools in the shed.
They do come in handy from time to time and 
are available from the default tray icon menu.

- [`KeyHistory`](https://www.autohotkey.com/docs/commands/KeyHistory.htm): Unsure how to bind a certain key/mouse button? Use this tool!
- [`ListVars`](https://www.autohotkey.com/docs/commands/ListVars.htm): Lists all variable names with content. Good for primitives (ie: an Array displays `Object object {address: 0x5B2AD0}`).
- [`ListLines`](https://www.autohotkey.com/docs/commands/ListLines.htm): Script lines most recently executed.
- [`ListHotkeys`](https://www.autohotkey.com/docs/commands/ListHotkeys.htm): Very crude listing of all registered hotkeys.

Scripts to [retrieve and parse the output for these windows](https://www.autohotkey.com/boards/viewtopic.php?f=6&t=9656&p=248727).


### Window Spy

Activate scripts only in certain contexts with `IfWinActive`. 
But how to determine this `ahk_class` thingy?

```autohotkey
; Esc twice to open/close DevTools... But only in Chrome
#IfWinActive, ahk_class Chrome_WidgetWin
~Esc::
If (A_PriorHotKey = A_ThisHotKey and A_TimeSincePriorHotkey < 500)
    Send {F12}
Return
#IfWinActive
```

To figure that out (and much more!), bundled with Autohotkey comes the immensely useful
utility Window Spy (usually called `AU3_Spy.exe`). It's also available from
the tray menu.


## Warnings

```autohotkey
; Default: Disable all warnings.
#Warn All, Off

; Every warning in a MsgBox.
#Warn

; Warn when a local variable is used before it's set in OutputDebug.
#Warn UseUnsetLocal, OutputDebug
```

Warning Types:  
- UseUnsetLocal and UseUnsetGlobal
- LocalSameAsGlobal
- ClassOverwrite
- UseEnv: Warn when a variable used has the same name as an environment variable. 
To avoid this confusion, start with [`#NoEnv`](https://autohotkey.com/docs/commands/NoEnv.htm) and
use [`EnvGet`](https://autohotkey.com/docs/commands/EnvGet.htm)
/ [`EnvSet`](https://autohotkey.com/docs/commands/EnvSet.htm) for interacting with
environment variables instead.


## OutputDebug

```autohotkey
OutputDebug, Value of %var%
```

If the script's process has no debugger, the system debugger displays the string.
Follow `OutputDebug` with, for example DebugView (`Dbgview.exe`) 
from [Sysinternals](https://docs.microsoft.com/en-us/sysinternals/).


## Suspension

### Pause

[`Pause`](https://www.autohotkey.com/docs/commands/Pause.htm) will toggle the current thread.
Hotstrings and Hotkeys will still run.
`A_IsPaused` contains the pause state of the underlying thread.

```autohotkey
; Freezes the current thread with Windows + Pause
#Pause::Pause, On
; Also: Off and Toggle (default)
```


### Suspend

[`Suspend`](https://www.autohotkey.com/docs/commands/Suspend.htm) will enable/disable all hotkeys and hotstrings.
`A_IsSuspended` contains the script suspension state.

```autohotkey
^#Pause::
; Suspend, On/Off/Toggle (default)
Suspend
MsgBox % "Hotkeys & hotstrings are now " (A_IsSuspended ? "disabled" : "enabled")
Return


#A::
Suspend, Permit
MsgBox I will run even when the script is suspended
Return
```

By default the script can also be paused/suspended via its [tray icon menu](https://autohotkey.com/docs/commands/Menu.htm#Icon)
and a different tray icon is shown when toggled. Nothing is worse as you thinking
your code doesn't work as expected when its not even running :)


### Exit & ExitApp

The [`#Persistent`](https://www.autohotkey.com/docs/commands/_Persistent.htm) directive
will make a script not directly `Exit` after the auto-execute section. `Exit` merely 
exits the current thread. Use `ExitApp` to close the script completely.

A script is automatically persistent when:  
- It has defined hotkeys or hotstrings
- It sets NumLock, ScrollLock or CapsLock AlwaysOn or AlwaysOff

Use `#Persistent` when you do not have any of the above but you do have timers or custom menu items.


```autohotkey
; Control + Win + Alt + Pause: Emergency exit
^#!Pause::
Suspend, Permit
ExitApp
Return


; Can register multiple OnExit
OnExit("ExitFunc")

ExitFunc(ExitReason, ExitCode)
{
  if ExitReason not in Logoff,Shutdown
  {
    MsgBox, 4, , Are you sure you want to exit?
    IfMsgBox, No
      return 1  ; Return non-zero to prevent exit.
  }
}
```

[ExitReasons](https://www.autohotkey.com/docs/commands/OnExit.htm):  
- Logoff: User logs off
- Shutdown: Computer shuts down (detect and abort a shutdown with `OnMessage(0x11, "WM_QUERYENDSESSION")`)
- Close: A `WM_CLOSE` or `WM_QUIT` message
- Error
- Menu: From the tray icon menu
- Commands: `Exit`, `ExitApp`, `Reload` and [`#SingleInstance`](https://www.autohotkey.com/docs/commands/_SingleInstance.htm)





# Custom Scripts

## Autoreload on save

Reload the Autohotkey script on save when working on it in your favourite editor.

```autohotkey
; Trigger on Control + S
; ~ = Also execute native function
~^s::
; Run even when script is suspended
Suspend, Permit

; Only in Sublime Text and Visual Studio Code
if (!WinActive("ahk_class PX_WINDOW_CLASS") and !WinActive("ahk_exe Code.exe"))
  return

; Only when the script dir/filename is in the titlebar
WinGetActiveTitle, winTitle
if (InStr(winTitle, A_Scriptdir) or InStr(winTitle, A_ScriptName))
  Reload

; Only when the top dir name is in the titlebar
SplitPath, A_Scriptdir, topDir
if (InStr(winTitle, topDir))
  Reload

return
```



## InstaHelp

Should your IntelliSense not cut it, open the online docs on the selected Autohotkey keyword with `Capslock + A`.
If it doesn't match a keyword, google "Autohotkey _selected text_".

<!-- TODO: Change code visualizer so that hiding parts of a code block is possible (all autohotkey commands list below) -->


```autohotkey
Capslock & A::
; Get the currently selected text
; (with some clipboard juggling:)
old := clipboard
clipboard =
Send, ^c
ClipWait, 3
needle := clipboard
clipboard := old

ahkCommands =
( LTRIM
  Break,Catch,Click,ClipWait,Continue,Control,ControlClick,ControlFocus,ControlGet,ControlGetFocus, ControlGetPos,ControlGetText,ControlMove, ControlSend,ControlSendRaw,ControlSetText,CoordMode,Critical, DetectHiddenText,Drive,DriveGet,DriveSpaceFree,Edit,Else,EnvAdd,EnvDiv,EnvGet, EnvMult,EnvSet,EnvSub, EnvUpdate,Exit,ExitApp,FileAppend,FileCopy,FileCopyDir,FileCreateDir,FileCreateShortcut,FileDelete, FileInstall,FileGetAttrib, FileGetShortcut,FileGetSize,fileGetVersion,FileMove,FileMoveDir,FileOpen, FileRead,FileReadLine,FileRecycle,FileRecycleEmpty,FileRemoveDir, FileSelectFile,FileSelectFolder, FileSetAttrib,FileSetTime,Finally,For,Format,FormatTime,GetKeyState,Gosub,Goto,GroupActive,GroupAdd, GroupClose,GroupDeactivate,Gui,GuiControl,GuiControlGet,Hotkey,if,IfEqual,IfNotEqual,IfExist,IfNotExist, IfGreater,IfGreaterOrEqual,IfInString, IfNotInString,IfLess,IfLessOrEqual,IfMsgBox,IfWinActive,IfWinNotActive, IfWinExist,IfWinNotExist,ImageSearch,IniDelete,IniRead,IniWrite,Input, InputBox,KeyHistory,KeyWait,ListHotkeys, ListLines,ListVars,Loop,Menu,MenuGetHandle,MenuGetName,MouseClick,MouseClickDrag,MouseGetPos,MouseMove,MsgBox, OnExit,OutputDebug,Pause,PixelGetColor,PixelSearch,PostMessage,Process,Progress,Random,RegDelete,RegRead,RegWrite, Reload,Return,Run,RunAs,RunWait,Send,SendRaw,SendInput,SendPlay,SendEvent,SendLevel,SendMessage,SendMode, SetBatchLines,SetCapsLockState,SetControlDelay,SetDefaultMouseSpeed,SetEnv,SetFormat,SetKeyDelay,SetMouseDelay, SetNumLockState,SetScrollLockState,SetregView,SetStoreCapsLockMode,SetTimer,SetTitleMatchMode,SetWinDelay, SetWorkingDir,Shutdown,Sleep,Sort,SoundBeep,SoundGet,SoundGetWaveVolume,SoundPlay,SoundSet,SoundSetWaveVolume, SplashImage,SplashTextOn,SplashTextOff,SplitPath,StatusBarGetText,StatusBarWait,StringCaseSense,StringLeft, StringRight,StringLower,StringReplace,StringSplit,StringTrimLeft,StringTrimRight,StringUpper,Suspend,SysGet, Thread,Throw,ToolTip,Transform,TrayTip,Until,UrlDownloadToFile,While,WinActivate,WinActivateBottom,WinClose, WinGetActiveStats,WinGetActiveTitle,WinGetClass,WinGet,WinGetPos,WinGetText,WinGetTitle,WinHide,WinKill,WinMaximize, WinMenuSelectItem,WinMinimize,WinMinimizeAll,WinMinimizeAllUndo,WinMove,WinRestore,WinSet,WinSetTitle,WinShow, WinWait,WinWaitActive,WinWaitClose,WinWaitNotActive,ClipboardTimeout,CommentFlag,Delimiter,DerefChar,ErrorStdOut, EscapeChar,HotkeyInterval,HotkeyModifierTimeout,Hotstring,Include,IncludeAgain,InputLevel,InstallKeybdHook, InstallMouseHook,LTrim,MaxHotkeysPerInterval,MaxMem,MaxThreads,MaxThreadsBuffer,MaxThreadsPerHotkey, MenuMaskKey,NoEnv,NoTrayIcon,Persistent,SingleInstance,UseHook,Warn,WinActivateForce
)

if (IsFunc(needle) or InStr(ahkCommands, needle)) {
  Run https://autohotkey.com/docs/commands/%needle%.htm
} else {
  Run, https://www.google.com/search?q=autohotkey+%needle%
}
return
```

Script to [open Autohotkey.chm instead](https://www.autohotkey.com/docs/scripts/ContextSensitiveHelp.htm).



## Other

[`A_ScriptHwnd`](https://www.autohotkey.com/docs/Variables.htm#ScriptHwnd)
contains the unique ID (HWND) of the script's hidden main window.

- [An object TreeViewer](https://autohotkey.com/board/topic/85201-array-deep-copy-treeview-viewer-and-more/) and more
- [Array_Gui](https://www.autohotkey.com/boards/viewtopic.php?f=6&t=35124&p=162979): Show arrays as an interactive TreeView.
- [Print Array](https://autohotkey.com/board/topic/70490-print-array/)
- [A Console to log to](https://www.autohotkey.com/boards/viewtopic.php?f=6&t=64724&p=277801)
- [Joystick Test Script](https://www.autohotkey.com/docs/scripts/JoystickTest.htm): Find out how to bind Jostick buttons
- [Autohotkey IntelliSense](https://www.autohotkey.com/docs/scripts/IntelliSense.htm): If your editor has no intellisense, switch editors instead :)

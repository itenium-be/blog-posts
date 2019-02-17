---
layout: post
author: Wouter Van Schandevijl
title:  "Autohotkey DynaRun"
date:   2019-02-17 00:00:00
desc: >
  Run any Autohotkey script on the web with a
  single.. Autohotkey hotkey.
img: autohotkey-dyna-run.jpg
imgdesc: ""
extras:
  - desc: autohotkey-dyna-run-capslock-f5.ahk
    url: /assets/blog-images/autohotkey-dyna-run-capslock-f5.ahk
categories: productivity
tags: [autohotkey]
toc:
  title: Autohotkey DynaRun
  icon: car
---

Run any Autohotkey script on the web with a
single.. Autohotkey hotkey.

<!--more-->


# Some Autohotkey blog posts

Some blog posts that have Autohotkey scripts for you to test out right away!

<!-- Jekyll Deprecation warnings: Apparently open issue on Github (auto closed) when working with subdirectories in _posts -->
- [Fill in an entire form with a few keystrokes]({% post_url 2019-01-31-autohotkey-use-case %})
- [Customize Windows Explorer]({% post_url 2017-04-22-advanced-windows-explorer %})
- [Hotkeys tutorial]({% post_url 2018-05-03-autohotkey-hotkeys %})


# The DynaRun script

Select an Autohotkey script and press `Capslock + F5`

```autohotkey
lastDynaRun :=


CAPSLOCK & F5::
Clipboard =
Send, ^c
ClipWait, 3


dynaRunName = Dynarun
if (lastDynaRun) {
    Process, Exist, %lastDynaRun%
    if (ErrorLevel > 0) {
        MsgBox, 4, Existing DynaRun, Replace last DynaRun?`nYes: Replace instance.`nNo: New instance., 1
        IfMsgBox, No
        {
            Random, rnd, 1, 100
            dynaRunName .= rnd
        }
        else
        {
            Process, Close, %lastDynaRun%
        }
    }
}

lastDynaRun := DynaRun(clipboard, dynaRunName)
return


DynaRun(TempScript, pipename="")
{
   static _:="uint",@:="Ptr"
   If pipename =
      name := "AHK" A_TickCount
   Else
      name := pipename
   __PIPE_GA_ := DllCall("CreateNamedPipe","str","\\.\pipe\" name,_,2,_,0,_,255,_,0,_,0,@,0,@,0)
   __PIPE_    := DllCall("CreateNamedPipe","str","\\.\pipe\" name,_,2,_,0,_,255,_,0,_,0,@,0,@,0)
   if (__PIPE_=-1 or __PIPE_GA_=-1)
      Return 0
   Run, %A_AhkPath% "\\.\pipe\%name%",,UseErrorLevel HIDE, PID
   If ErrorLevel
      MsgBox, 262144, ERROR,% "Could not open file:`n" __AHK_EXE_ """\\.\pipe\" name """"
   DllCall("ConnectNamedPipe",@,__PIPE_GA_,@,0)
   DllCall("CloseHandle",@,__PIPE_GA_)
   DllCall("ConnectNamedPipe",@,__PIPE_,@,0)
   script := (A_IsUnicode ? chr(0xfeff) : (chr(239) . chr(187) . chr(191))) TempScript
   if !DllCall("WriteFile",@,__PIPE_,"str",script,_,(StrLen(script)+1)*(A_IsUnicode ? 2 : 1),_ "*",0,@,0)
        Return A_LastError,DllCall("CloseHandle",@,__PIPE_)
   DllCall("CloseHandle",@,__PIPE_)
   Return PID
}
```

Or download [dyna-run-capslock-f5.ahk](/assets/blog-images/autohotkey-dyna-run-capslock-f5.ahk)

# Installing the script

- Download and [install Autohotkey](https://www.autohotkey.com/download) (Pick Autohotkey 1.1)
- Create a file with the extension **ahk**
- Paste the Autohotkey code from above in the file
- Double click your newly created file to activate the script
- Select some Authotkey text and press `Capslock + F5`

<br>
Mega test script:  
```autohotkey
MsgBox Will it work?
```

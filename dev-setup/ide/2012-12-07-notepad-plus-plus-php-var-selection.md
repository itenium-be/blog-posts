---
layout: post
author: Wouter Van Schandevijl
title:  "Notepad++ double click PHP variable including the $ sign"
date:   2012-12-07
desc: >
  Like toggling a checkbox these days.
img:
  url: notepad-php-vars.jpg
  desc: "Photo by Sharon McCutcheon"
  origin: https://unsplash.com/photos/8lnbXtxFGZw
categories: dev-setup
tags: []
toc:
  title: Built-in
  icon: hand-point-up
updates:
  - date: 2019-06-21
    desc: "Built-into Notepad++ for some time now"
---

Built-in in Notepad++ since 2016:

**Settings > Preferences > Delimiter > Add you character as part of word > $**


<!--more-->

# Old, Broken and Obsolete Authotkey solution

My previous editor Code-Genie already selected the $ sign along with the variable name when I double clicked somewhere
on a word. When I upgraded to Notepad++, I was confused this was no longer happening as it seemed pretty obvious to me that
when writing PHP almost always you’d want the $ also. Apparently not that many agree, but I want that behavior so I checked
the StackOverflow answers. The follow up answer provides a workaround using a Notepad++ macro and an Autohotkey script.
The problem is that it requires you to use a special keybinding (alt+click) to trigger the macro.
I still want my Code-Genie feeling back.

With Notepad++ seemingly letting us down, I was sure it was Autohotkey to the rescue:

```autohotkey
; Also select the $ when control clicking a word in a Notepad++ PHP file
^LButton::
WinGetActiveTitle, windowTitle
isNotepadPhpFile := RegExMatch(windowTitle, "\.[pP][hH][pP] - Notepad\+\+$")
if isNotepadPhpFile
{
    Send, ^{Click}
    return
}
 
; Selects the word the cursor is on plus an extra character on the left
Click
Send, ^{LEFT}{LEFT}+{RIGHT}^+{RIGHT}
return
```

Wait… You promised a double-click solution and now post code bound to control+click, what’s going on here?
Well, I cracked out some code – literally having to lookup every keyword along the way – until I got to the
point where I decided to continue the fight another day. I got to a solution with the following problems:

- The cursor is jumping around because I'm sending keystrokes.
- Clicking twice in succession but on different lines is interpreted as a double click.
- You have to wait a little between double and triple clicks (select entire line) for this to work correctly.

I will update the code to fix these issues and revisit this post then. Here is the already "somewhat-workable code" :)

```autohotkey
; Also select the $ when double clicking a word in a Notepad++ PHP file
~LButton::
WinGetActiveTitle, windowTitle
isNotepadPhpFile := RegExMatch(windowTitle, "\.[pP][hH][pP] - Notepad\+\+$")
if !isNotepadPhpFile
    return
 
if (A_PriorHotkey <> "~LButton" or A_TimeSincePriorHotkey > 300)
{
    ; Too much time between clicks, so this isn't a double-click.
    ; Flawed: If you click twice in succession on different rows
    ;         it thinks it's a double click while really it is not.
    KeyWait, LButton
    return
}
 
; 1: Less jumping doesn't preserve the cursor position
; 0: More jumping. You actually see the cursor moving (even more:)
settingLeastCursorJumping = 1
 
; Selects the word the cursor is on plus an extra character on the left
IfEqual settingLeastCursorJumping, 1, Send, ^{LEFT}{LEFT}+{RIGHT}^+{RIGHT}
IfNotEqual settingLeastCursorJumping, 1, Send, {RIGHT}^+{LEFT}+{LEFT}
 
; Resets the cursor position if the first character isn't $
var := GetCurrentlySelectedText()
if (InStr(var, "$") = 0)
{
    IfEqual settingLeastCursorJumping, 1, Send, {RIGHT}^{LEFT}^+{RIGHT}
    IfNotEqual settingLeastCursorJumping, 1, Send, +{RIGHT}
}
return
 
; Capture and return selected text to clipboard
; Restore clipboard's value after CTRL+C
GetCurrentlySelectedText()
{
    oldClipboard = %Clipboard%
    Send, ^c
    selectedText = %Clipboard%
    Clipboard = %oldClipboard%
    return selectedText
}
```

---
layout: post
author: Wouter Van Schandevijl
title:  "Autohotkey Use Case: Submitting a Form"
date:   2019-01-31 00:00:00
desc: >
  Wait, how did you just fill in that form?
  Autohotkey, that's how!
img: productivity.jpg
imgdesc: "Automate repetitive tasks with Autohotkey!"
extras:
  - desc: Test Form Html Page
    url: /assets/blog-images/autohotkey-login-form.html
categories: productivity
tags: [autohotkey]
toc:
  title: Autohotkey Use Case 
  icon: car
---

A simple Autohotkey use case you can start using in your application right away:  
Filling in an entire form with predefined values.

<!--more-->

# The Use Case

## The Form

Considering the following login form
([Html version](/assets/blog-images/autohotkey-login-form.html))

![desc](/assets/blog-images/autohotkey-login-form.png)



## The Script

The Autohotkey script:

```autohotkey
:*:lma::admin@mega-app.com{tab}my_super_secret_pwd{tab}{space}{tab}{enter}
```

<br>
Type `lma` (Short for `Login Mega App`?) while the cursor is in the email input
to fill in the form and submit it.


<!-- This block exists three times: autohotkey-dyna-run.md, autohotkey-use-case.md and autohotkey-login-form.html -->
<!-- ATTN: Some slight variations in install instructions!-->

# Autohotkey installation

- Download and [install Autohotkey](https://www.autohotkey.com/download) (Pick Autohotkey 1.1)
- Create a file with the extension **ahk**
- Paste the Autohotkey code from above in the file
- Double click your newly created file to activate the hotstring
- Press **lma** while the cursor is in the email input on [the test page](/assets/blog-images/autohotkey-login-form.html)

## To start with Windows

To start your Autohotkey script with Windows put a file shortcut in your startup folder. 

To get to this directory:

- Press `Windows + E`
- Press `Control + L`
- Type `shell:startup`
- Press `Enter`

Usually something like:

`C:\Users\your_user_name\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup`

With `your_user_name`: `$env:USERNAME` (PS1) or `%USERNAME%` (CMD)


# More info

## The Script Explained

<!-- TODO: put link here after writing Autohotkey - Hotstrings tutorial -->

```autohotkey
; `*` means the hotstring will expand as soon as it is typed in
; It can be omitted so the hotstring will only expand when followed by an `EndChars`
; The part between the double :: is the key sequence that will trigger the hotstring
::lma::

; Send some text
SendInput admin@mega-app.com

; Send special characters
SendInput {tab}{tab}{space}{tab}{enter}
return
```


## Using a hotstring instead

The same could also be triggered by pressing `Control + Windows + L` for example

```
^#l::
SendInput admin@mega-app.com{tab}my_super_secret_pwd{tab}{space}{tab}{enter}
return
```

See the [blog post on hotkeys](/blog/productivity/autohotkey-hotkeys/) for more hotkey info and possibilities.


## Context sensitive

When testing an application, I tend to use a different browser for each user profile so that I do
not have to logout/login all the time. We could even check the value of `document.title` and use different
credentials for different applications.

```autohotkey
; Type loginAdv to activate the hotstring
:*:loginAdv::
WinGetTitle, title, A

; These logins work only in Chrome
#IfWinActive, ahk_class Chrome_WidgetWin
    IfInString, title, ProjectDocumentTitle1
        SendInput admin{tab}pwd{enter}
    Else IfInString, title, ProjectDocumentTitle2
        SendInput superuser{tab}pwd{enter}
    Else
        SendInput root{tab}pwd{enter}
#IfWinActive

; Firefox
#IfWinActive, ahk_class MozillaWindowClass
    IfInString, title, ProjectDocumentTitle1
        SendInput client{tab}pwd{enter}
    Else IfInString, title, ProjectDocumentTitle2
        SendInput clerk{tab}pwd{enter}
    Else
        SendInput guest{tab}pwd{enter}
#IfWinActive
Return
```

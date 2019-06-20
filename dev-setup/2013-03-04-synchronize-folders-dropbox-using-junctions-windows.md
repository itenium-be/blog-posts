---
layout: post
author: Wouter Van Schandevijl
title:  "Synchronize Additional Folders With Dropbox Using Junction on Windows"
date:   2013-03-04
desc: >
  Praise for Dropbox and junction.exe tutorial.
img:
  url: dropbox-junction.png
categories: dev-setup
tags: []
toc:
  title: Dropbox
  icon: dropbox
---

[Dropbox](http://www.dropbox.com/) is great! I'm pretty sure you’ve already heard of it but for those who have not: It allows you to synchronize about 2 gig
(and up to 16 gig with some effort) worth of documents, pictures or other files between your different computers, laptops or other
mobile devices. The files are kept on your machine(s) and in the cloud, but they are also accessible from anywhere with a browser
and shareable with everyone you’d like.

<!--more-->

While it is very nicely integrated with Windows, one little caveat is that additional directories outside the Dropbox main folder can't
be synchronized out of the box.

[Junction](https://docs.microsoft.com/en-us/sysinternals/downloads/junction), a free download from [sysinternals.com](http://www.sysinternals.com/),
fixes this problem. Once a junction between an existing directory and a new one somewhere inside the Dropbox folder has been established, any change 
to either directory gets immediately synchronized with Dropbox…


# To create a junction.

The first parameter is the directory you want to create, the second parameter is the existing directory you want to link. Windows changes
the icon of a junction folder to indicate its nature but when you have linked to a Dropbox folder, it is overwritten by the Dropbox
synchronization icons. You could move the actual directory to Dropbox and link to the original location if you want the junctions to be visible
in Windows explorer.

```bash
junction "newlyCreatedJunctionPath" "existingOriginalPath"
junction "C:\Users\Documents\Dropbox\www" "c:\wamp\www"
```

# To list all existing junction points

Use `–s` for recursive search and `–q` to stop displaying access denied stuff. Aside from you custom made links this will also display
a whole bunch of already made junction points related to the current Windows account.

```bash
junction -s -q c:\
```

# To delete an existing junction.

Deleting an entire junction folder will keep the original directory intact. So it's probably easier to just delete the junction point in Explorer.
If you want to get rid of everything, merely deleting the original directory will leave the junction "hanging".

```bash
junction -d "C:\Users\Documents\Dropbox\www"
```

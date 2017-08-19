---
layout: post
title:  "Window splitting in Cmder"
date:   2017-04-12 15:00:00 +0200
categories: productivity
tags: [powershell,cmder]
---


<!--more-->

https://conemu.github.io/en/NewConsole.html#example-7
https://conemu.github.io/en/Tasks.html
https://conemu.github.io/en/SplitScreen.html


-new_console:
a - RunAs admin
d:"<dir>" - specify working directory
s[<SplitTab>T][<Percents>](H|V) - run new console in a split
t:"<tabname>" - rename new created tab
C:"<iconfile>" - specify icon used in tab

Examples:

Create new tab 50% width right of current tab
cmd -new_console:s50H



>powershell -new_console:d:"C:\projects\Digipolis\Container":t:"Container"
powershell -new_console:d:"C:\projects\Digipolis\Shared":t:"Shared"
powershell -new_console:d:"C:\projects\Digipolis\Debian":t:"Debian"
powershell -new_console:d:"C:\projects\Digipolis\Note\App":t:"Note.App"
powershell -new_console:d:"C:\projects\Digipolis\Note\Service":t:"Note.Service"


>cmd /k ""%ConEmuDir%\..\init.bat" & nodemon" -cur_console:n:d:"C:\projects\Digipolis\Container":t:"Focus-Nodemon"
cmd /k ""%ConEmuDir%\..\init.bat" & gulp" -cur_console:s1TVn:d:"C:\projects\Digipolis\Container":t:"Focus-gulp"
cmd /k ""%ConEmuDir%\..\init.bat" & gulp" -cur_console:s1THn:d:"C:\projects\Digipolis\Wtm\App\Focus.Wtm.App":t:"Wtm-gulp"
cmd /k ""%ConEmuDir%\..\init.bat" & gulp" -cur_console:s2THn:d:"C:\projects\Digipolis\FedPol\App\Focus.FedPol.App":t:"FedPol-gulp"


Setting up tasks
----------------


PsReadLine Integration
----------------------
Rename-Tab -> Another post with Posh-Git, PsReadLine







Ga naar Settings > Startup > Tasks (Shortcut: Win + Alt + T)
 
Startup Task voor PitStop
 
>cmd /k ""%ConEmuDir%\..\init.bat" & ng serve" -cur_console:n:d:"C:\Macadam\pitstop\frontend":t:"PitStop-serve"
powershell -cur_console:s75Hn:d:"C:\Macadam\pitstop":t:"PitStop"
 
Startup Task voor Showroom
 
>cmd /k ""%ConEmuDir%\..\init.bat" & ng serve" -cur_console:n:d:"C:\Macadam\showroom\frontend":t:"Showroom-serve"
powershell -cur_console:s75Hn:d:"C:\Macadam\showroom":t:"Showroom"
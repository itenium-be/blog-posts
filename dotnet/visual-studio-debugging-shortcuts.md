---
layout: post
title:  "Visual Studio: Debugging Shortcuts"
date:   2018-08-15 00:00:00 +0200
categories: dotnet
tags: [visual studio,debugging,cheat-sheet]
---

{% include toc title="Debugging" icon="dot-circle-o" %}

## The Basics

{% include kbd k="F5" l="Start debugging" %}
{% include kbd k="Control+F5" l="Start without debugging" %}
{% include kbd k="Shift+F5" l="Stop debugging" %}
{% include kbd k="Control+Shift+F5" l="Stop, rebuild and start debugging" %}
{% include kbd k="Control+Alt+P" l="Attach to process" %}
<br>
{% include kbd k="F10" l="Step over" %}
{% include kbd k="F11" l="Step into" %}
{% include kbd k="Shift+F11" l="Step out" %}
{% include kbd k="Control+F10" l="Run to cursor" %}
{% include kbd k="Control+Shift+F10" l="Set next statement" %}
{% include kbd k="Alt+Num *" l="See current statement" %}
<br>

<!--more-->


## Breakpoints

{% include kbd k="Control+Alt+B" l="Breakpoints window" %}
{% include kbd k="F9" l="Toggle breakpoint" %}
{% include kbd k="Control+F9" l="Enable/Disable breakpoint" %}
{% include kbd k="Control+Shift+F9" l="Delete all breakpoints" %}

{% include kbd k="Control+B" l="New function breakpoint dialog" %}
Useful when you want to add a breakpoint to every method with a specific name.

{% include kbd k="Alt+F9, C" l="Breakpoint condition" %}
{% include kbd k="Alt+F9, L" l="Breakpoint label" %}
Assigning labels to breakpoints could be handy together with the export/import functionality?


## Inspection

Hover over a variable to see its value. Double click on it to 
change its value. Override `ToString()` or use the 
`DebuggerDisplayAttribute()` to display something better than
`GetType().FullName` for your own classes.

```c#
[DebuggerDisplay("Count={count}")]
class Counter
{
    public int count = 4;
}
```

{% include kbd k="Control+Alt+I" l="Immediate window" %}
{% include kbd k="Shift+F9" l=" QuickWatch window for variable on cursor (blocking)" %}
{% include kbd k="Control+Alt+C" l="Call stack window" %}
{% include kbd k="Control+Alt+W, 1-4" l="Watch windows 1-4" %}
{% include kbd k="Control+Alt+A" l="Command window" %}

TODO: Control+Alt+ : Locals window --> Need to set this one
Immediate window?

Enable "Edit & continue" in Tools > Options > Debugging

{% include kbd k="Control+Alt+E" l="Exceptions window" %}

Trace.Write etc to Output window (can be configured to write other stuff to Output window in Exceptions window)

Trace.WriteIf

Debug.Assert(true, "faster than a ")

Debug.Fail(); // a breakpoint in code

[ConditionalAttribute("DEBUG")]
--> Can be used for assertions that don't fit on one line



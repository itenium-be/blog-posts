---
layout: post
author: Wouter Van Schandevijl
title:  "Visual Studio: Debugging Shortcuts"
date:   2018-08-15 00:00:00 +0200
img: debugging.png
desc: >
    We obviously never encounter bugs in our code.
    Still, should you ever need to debug, use these
    shortcuts so that it may be over soon.
categories: dotnet
tags: [visual studio,debugging,cheat-sheet]
toc:
    title: Debugging
    icon: bug
---


visual-studio-debugging-run-to-click

# The Basics
{: .hide-from-excerpt}

{% include kbd k="F5" l="Start debugging" %}
{% include kbd k="Control+F5" l="Start without debugging" %}
{% include kbd k="Shift+F5" l="Stop debugging" %}
{% include kbd k="Control+Shift+F5" l="Stop, rebuild and start debugging" %}
{% include kbd k="Control+Alt+P" l="Attach to process" %}
<br>
{% include kbd k="Control+Alt+Break" l="Break (Debug > Break All)" %}
{% include kbd k="F10" l="Step over" %}
{% include kbd k="F11" l="Step into" %}
{% include kbd k="Shift+F11" l="Step out" %}
{% include kbd k="Control+F10" l="Run to cursor" %}
{% include kbd k="Control+Shift+F10" l="Set next statement" %}
{% include kbd k="Alt+Num *" l="See current statement" %}


<br>

<!--more-->

Enable "Edit & continue" in Tools > Options > Debugging.  
Other interesting settings:  
- Step over properties and operators (Managed only)
- Just My Code (Enable to step over system, framework and other non-user calls)


# Breakpoints

{% include kbd k="Control+Alt+E" l="Control on which exceptions to break with the Exceptions window" %}

{% include kbd k="Control+Alt+B" l="Breakpoints window" %}
{% include kbd k="F9" l="Toggle breakpoint" %}
{% include kbd k="Control+F9" l="Enable/Disable breakpoint" %}
{% include kbd k="Control+Shift+F9" l="Delete all breakpoints" %}

{% include kbd k="Control+B" l="New function breakpoint dialog" %}
Useful when you want to add a breakpoint to every method with a specific name.

{% include kbd k="Alt+F9, C" l="Breakpoint condition" %}
{% include kbd k="Alt+F9, L" l="Breakpoint label" %}
Assigning labels to breakpoints could be handy together with the export/import functionality?


<br>
Breakpoints in `DEBUG` mode only:  
```c#
using System.Diagnostics;
Debug.Fail();
Debug.Assert(true, "Conditional break");
```

<br>
Debugger attributes:  
- DebuggerStepThroughAttribute: Makes F11 on a member behave like F10
- DebuggerHiddenAttribute: Hide the member from the debugger
- DebuggerNonUserCodeAttribute: Combine the two above

<br>
When you need to do some calculations for the assertions
put it in a method decorated with the `[ConditionalAttribute("DEBUG")]`.



# Inspection

Hover over a variable to see its value. Double click on it to 
change its value. Override `ToString()` or use the 
`DebuggerDisplayAttribute()` to display something better than
`GetType().FullName` for your own classes.


<br>
**Attributes**:  

```c#
[DebuggerDisplay("Count={count}")]
class Counter
{
    [DebuggerBrowsable(DebuggerBrowsableState.Never)]
    public int count = 4;

    [DebuggerBrowsable(DebuggerBrowsableState.RootHidden)]
    public int[] y => new[] {1, 2, 3};
}
```

## Windows

{% include kbd k="Shift+F9" l="QuickWatch window for variable on cursor (blocking)" %}
{% include kbd k="Control+Alt+C" l="Call stack window" %}
{% include kbd k="Control+Alt+W, 1-4" l="Watch windows 1-4" %}
{% include kbd k="Control+Alt+A" l="Command window" %}


### Locals and Autos

Open from menu:  
- Debug > Windows > Locals
- Debug > Windows > Autos

Can see member return values in the Autos window.  
Text in red: variable value changed since last evaluation.  


### Output Window

Menu: Debug > Windows > Output  
Control what is written to the Output Window with Tools > Options > Debugging > Output Window

```c#
Debug.Write("msg");
Debug.WriteIf(true, "msg");
Debug.WriteLine("msg");
Debug.WriteLineIf(false, "msg");
```

### Immediate window

{% include kbd k="Control+Alt+I" l="Immediate window" %}

Can access local variables and call methods. Even Linq queries
are possible here. 



--> Visualizers & DebuggerTypeProxy to its own article?

# Visualizers

## Plugins

**ReadableExpressions**:  
- [Github](https://github.com/agileobjects/ReadableExpressions)
- [Plugin](https://marketplace.visualstudio.com/items?itemName=vs-publisher-1232914.ReadableExpressionsVisualizers)

[DataTable Visualizer](https://marketplace.visualstudio.com/items?itemName=ShineTools.DataTableVisualizer)
[Debugger Image Visualizer](https://marketplace.visualstudio.com/items?itemName=AleksanderBerus.DebuggerImageVisualizerPreview)

LINQ Insight Express ORM Profiler
https://marketplace.visualstudio.com/items?itemName=DevartSoftware.LINQInsightExpress

Entity Visualizer
https://marketplace.visualstudio.com/items?itemName=KirillPolishchuk.EntityVisualizer

Linq to Entity query visualizer
https://marketplace.visualstudio.com/items?itemName=RRV.LinqtoEntityqueryvisualizer


Mole
https://marketplace.visualstudio.com/items?itemName=KarlShifflettkdawg.MoleforVisualStudio2017

LINQBridgeVs
https://marketplace.visualstudio.com/items?itemName=codingadventures.linqbridgevs






Not Free:
- [OzCode](https://marketplace.visualstudio.com/items?itemName=CodeValueLtd.OzCode)

https://www.oz-code.com/
--> Check it out, looks pretty cool.


## DebuggerTypeProxy



https://www.codeproject.com/Articles/13127/Create-a-Debugger-Visualizer-in-10-Lines-of-Code
https://msdn.microsoft.com/en-us/library/zayyhzts.aspx?f=255&MSPPError=-2147217396



--> Get this info from other existing blog post?



# New in Visual Studio 2017

**Run to click**:  
![Run to click](/assets/blog-images/visual-studio-debugging-run-to-click.png)

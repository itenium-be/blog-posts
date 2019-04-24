---
layout: post
author: Wouter Van Schandevijl
title:  "Debugging in Visual Studio"
date:   2019-02-24 00:00:00
img:
  url: debugging.png
desc: >
  We obviously never encounter bugs in our code.
  Still, should you ever need to debug, use these
  shortcuts so that it may be over soon.
categories: dotnet
tags: [tutorial,cheat-sheet,debugging]
interesting:
  - url: https://docs.microsoft.com/en-us/visualstudio/debugger/debugger-feature-tour
    desc: "Official Visual Studio Debugger Tutorial"
toc:
  title: Debugging
  icon: bug
---


# Basic Shortcuts
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

Enable "**Edit & continue**" in Tools > Options > Debugging.  
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

# Attributes

- [`DebuggerStepThroughAttribute`][DebuggerStepThroughAttribute]: Makes F11 on a member behave like F10
- [`DebuggerHiddenAttribute`][DebuggerHiddenAttribute]: Hide the member from the debugger
- [`DebuggerNonUserCodeAttribute`][DebuggerNonUserCodeAttribute]: Combine the two above

When you need to do some calculations for your assertions
put it in a method decorated with the [`ConditionalAttribute("DEBUG")`][ConditionalAttribute].



# Inspection

Hover over a variable to see its value. Double click on it to 
change its value. Override `ToString()` or use the 
[`DebuggerDisplayAttribute()`][DebuggerDisplayAttribute] to display something better than
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

More control with [`DebuggerTypeProxy`][DebuggerTypeProxy]:

Use this attribute when you need to significantly and fundamentally change the debugging view of a type, but not change the type itself.
{: .notice--info}


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



# Visualizers

One of my favourite bloggers, [Phil Haack created the Visual Studio visualizer Encourage](https://haacked.com/archive/2014/06/20/encourage-vs/).

{% include github-stars.html url="haacked/encourage" desc="A bit of encouragment added to Visual Studio" %}



## Plugins

**ReadableExpressions** ([download](https://marketplace.visualstudio.com/items?itemName=vs-publisher-1232914.ReadableExpressionsVisualizers))  
{% include github-stars.html url="agileobjects/ReadableExpressions" desc="Readable views of Expression Trees" %}


<br>
**DataTable Visualizer** ([download](https://marketplace.visualstudio.com/items?itemName=ShineTools.DataTableVisualizer))  
{% include github-stars.html url="MgSam/DataTableVisualizer" desc="A non-modal, dockable, searchable DataTable visualizer" %}


<br>
**Image Visualizer** ([download](https://marketplace.visualstudio.com/items?itemName=AleksanderBerus.DebuggerImageVisualizerPreview))  
{% include github-stars.html url="aberus/ImageVisualizer" %}


<br>
**Entity Visualizer** ([download](https://marketplace.visualstudio.com/items?itemName=KirillPolishchuk.EntityVisualizer))  
{% include github-stars.html url="kpol/EntityVisualizer" desc="Display SQL source code generated by Entity Framework" %}

<!-- <br> -->
<!-- **Linq to Entity query visualizer** ([download](https://marketplace.visualstudio.com/items?itemName=RRV.LinqtoEntityqueryvisualizer))   -->


<br>
**LINQBridgeVs** ([download](https://marketplace.visualstudio.com/items?itemName=codingadventures.linqbridgevs))  
{% include github-stars.html url="codingadventures/LINQBridgeVs" desc="Bridge between a Visual Studio debugging session and LINQPad." %}


<br>
Not open source:  
- **Mole** ([download](https://marketplace.visualstudio.com/items?itemName=KarlShifflettkdawg.MoleforVisualStudio2017))  
- **DevArt LINQ Insight Express ORM Profiler** ([download](https://marketplace.visualstudio.com/items?itemName=DevartSoftware.LINQInsightExpress))  



# New in Visual Studio

## Visual Studio 2017

**Run to click**:  
![Run to click](/assets/blog-images/visual-studio-debugging-run-to-click.png)


## Visual Studio 2019

- Promises "significant" performance improvements in stepping
- Yipiee, they added search to Autos, Locals and Watch windows! (Options > Debugging > Search)
- They also managed to cut 25px from the title bar and still make it look [pretty slick](/assets/blog-images/visual-studio-2019-look.png).

[DebuggerStepThroughAttribute]: https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.debuggerstepthroughattribute?view=netcore-3.0
[DebuggerHiddenAttribute]: https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.debuggerhiddenattribute
[DebuggerNonUserCodeAttribute]: https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.debuggernonusercodeattribute
[ConditionalAttribute]: https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.conditionalattribute
[DebuggerDisplayAttribute]: https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.debuggerdisplayattribute
[DebuggerTypeProxy]: https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.debuggertypeproxyattribute

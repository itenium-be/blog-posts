---
layout: post
title:  "VB.NET vs C# Syntax differences"
date:   2017-04-15 15:00:00 +0200
categories: dotnet
tags: [net,cheat-sheet]
---

{% include toc title="Battle" icon="dot-circle-o" %}

A cheat sheet outlining the syntax differences between VB.NET and C#. This post is written
specifically for advanced C# developers with little to no VB.NET knowledge who need to do
some VB.NET coding.

Because some things are just so similar but still confusingly different in VB.NET vs C#...
{: .notice--info}

Along the way we'll make some amazing discoveries like:
- There are things VB.NET just can't do (pointers, ...)
- There are a few cases where VB.NET code is *shorter* than it's C# counterpart (gasp!)
- VB.NET defaults are not what you might expect from a strongly typed language due to its VB6 legacy (Option Strict, Explicit)
- There is some VB.NET only stuff that comes in handy from time to time

<!--more-->


Confusingly different
---------------------
Or some fun issues during heavy context switching :)

- Terminating statements with `;` in VB.NET or not using `_` for line continuations.
- Compare with `=` in C# or with `==` in VB.NET (and `!=` vs `<>`)
- VB.NET is *not* case sensitive
- Arrays in VB.NET are 1 based

<br>

* * *


Syntax comparison
-----------------

### General syntax

```vb
' VB.NET
#If DEBUG Then
#End If
#Region "Imports"
Imports System
#End Region
Namespace MyApp
	<Serializable>
	Public Class HappyCoding
		''' <summary>
		''' XML Comment
		''' </summary>
		Shared Sub Main(ByVal args() As String)
			Dim name As String = "VB.NET"
			Dim builder As New StringBuilder()

			' "X"c for defining a char
			builder.Append("A"c)

			' Parentheses are optional when calling
			' a method without parameters
			' Array accessor is with parentheses (), not with []
			' All this allows for "fun" like this:
			Dim firstChar = builder.ToString.ToArray(0)
		End Sub
	End Class
End Namespace
```

C# equivalent

```c#
#if DEBUG
#endif
#region usings
using System;
#endregion
namespace MyApp {
	[Serializable]
	public class HappyCoding {
		/// <summary>
		/// XML Comment
		/// </summary>
		static void Main(string[] args) {
			string name = "C#";
			var builder = new StringBuilder();
			builder.Append('A');
			char firstChar = builder.ToString().ToArray()[0];
		}
	}
}
```

### Data Types

| VB.NET                              | C#
|-------------------------------------|--------------------------|
| Dim b As Byte = &H2A                | byte b = 0x2A;
| Dim c As Char = "B"c                | char c = 'c';
| Dim d As Decimal = 35.99@           | decimal d = 35.99m;
| Dim f As Single = 2.9!              | float f = 2.9f;
| Dim pi As Double = 3.1415           | double pi = 3.1415D;
| Dim lng As Long = 123456L ' UL      | long lng = 123456L; // UL
| Dim s As Short = 123S ' US          | short s = 123;
|                                     |
| Dim i? As Integer = Nothing         | int? i = null;
| Dim i As Integer?                   | int? i;
|-------------------------------------|--------------------------|
{: .table-code}

```vb
Dim d As Date = #10/15/2005# ' C#: var d = new DateTime(2005, 10, 15)
Dim dt As DateTime = #10/15/2005 12:15:00 PM#
```

This compiles!? How cool is VB.NET :)  
A VB.NET `Date` is in fact an alias for `DateTime`. 
So yes, `Date` does have a time part.



### Type Information

| VB.NET                          | C#
|---------------------------------|---------------------------------|
| Dim x As Integer                | int x;
| x.GetType()                     | x.GetType();
| GetType(Integer)                | typeof(int);
| TypeName(x)                     | x.GetType().Name;
| NameOf(StringBuilder)           | nameof(StringBuilder)
|                                 |                                 |
| Dim lng = CType(x, Long)        | var lng = Convert.ToInt64(x);
| Dim lng = CLng(x)               | var lng = Convert.ToInt64(x);
| Dim i = Int(x)                  | var i = Conversion.Int(x);
|                                 |                                 |
| Dim s As New Shape              | var s = new Shape();
| TypeOf s IsNot Circle           | !(s is Circle)
| s1 Is s2                        | Object.ReferenceEquals(s1, s2)
| Dim c = TryCast(s, Circle)      | var c = s as Circle;
| c = DirectCast(s, Circle)       | c = (Circle)s;
{: .table-code}

Aside from CLng, VB.NET also has: CInt, CDate, CStr, CByte, CDbl, CBool, CObj, Chr.
And more conversion methods in `Microsoft.VisualBasic.Conversion`. This is a Module
so `Conversion.Int(x)` is available as `Int(x)` without Import.


### Operators, Strings

| VB.NET                          | C#                              |
|---------------------------------|---------------------------------|
| Comparison
| <> =  <  >  <=  >=              | != ==  <  >  <=  >=  
| And   Or   Xor   Not   <<   >>  | &   \|   ^   ~   <<   >>
| AndAlso   OrElse  Not           | &&   \|\|   !
| x Is Nothing Or y IsNot Nothing | x == null \| y != null
|                                 |
| Arithmetic                      |
| +  -  *  /                      | Same
| Mod                             | %
| \  (integer division)           | 
| ^  (raise to a power)           | Math.Pow
|                                 |
| Assignment                      |
| =  +=  -=, ...                  | = += -=, ...
|                                 | ++ --
|                                 |
| Strings                         |
| Concatenation: &                | +
| Escape quote: ""                | \\"
| vbCrLf, vbTab, ...              | \r\n, \t, ...
{: .table-margin}


All logical comparisons in VB.NET are typically done with `AndAlso` and `OrElse`.
It's (surprise surprise) more typing than `And/Or` but the latter don't short-circuit
the evaluations. Ex: `If x IsNot Nothing And x.Trigger() Then` will crash.

To escape a reserved word `Public Class [Class]`. (C#: `public class @class`)

### Control flow

| VB.NET                          | C#
|---------------------------------|---------------------------------|
| Dim i = If(y, 5)                | var i = y ?? 5;
| Dim x = If(i > 0, "t", "f")     | var x = i > 0 ? "t" : "f"
{: .table-code}


```vb
If x < 0 Then res = "oeps" Else res = "yaye"

If x > 5 Then
	x *= y
ElseIf x = 5 OrElse y Mod 2 = 0
	' Then is optional
	x += y
Else
	x /= y
End If

' Microsoft.VisualBasic.CompilerServices.LikeOperator.LikeString
' Uses: Option Compare Binary/Text
If "John 3:16" Like "Jo[Hh]? #:*" Then
	' Use [!A-Z] for Regex [^A-Z]
	' VB.NET Like => Regex
	' # => \d, * => .*, ? => .
End If
' C#: Use Regex

' VB.NET switch is more feature rich
' There is no break; and no fallback
Select Case x
	Case 1, 2, 5 To 10
	Case Is = y ' Is is optional
	Case Is > 10
	Case Else
		' Exit Select is implied
End Select

Select Case x.GetType
	Case GetType(StringBuilder)
End Select

For i As Integer = 0 To arr.Length - 1 Step 3
	' Step 1 is the default
Next
// C#: for (i = 0; i < arr.Length; i += 3) {}

For Each n As String In names
	Exit For
	Continue For
Next

// C#
foreach (string n in names) {
	break;
	continue;
}
```



### Classes, Generics, Methods, Enums, ...

| VB.NET                 | C#
|----------------------  |---------------------------------|
| Public                 | public
| Private                | private
| Friend                 | internal
| Protected              | protected
|                        |                                 |
| Partial Class          | partial class
| MustInherit Class      | abstract class
| NotInheritable Class   | sealed class
| Module                 | static class
|                        |                                 |
| Methods
| MustOverride           | abstract
| NotInheritable         | sealed
| Shared                 | static
| Overridable            | virtual
| Overloads ' is optional| // no keyword required
{: .table-code}


```vb
Interface IAlarm
	Property WakeUpAt As DateTime
End Interface

Structure Student
End Structure

Enum Action
	Start = 1
	[Stop] ' Escape reserved words with []
End Enum
' enum Action {Start = 1, Stop}

Class MyColl(Of TEntity)
	Inherits ObjectModel.Collection(Of TEntity)
	Implements IDisposable, IAlarm
	' Class MyCall<TEntity> : Collection<TEntity>, IDisposable, IAlarm

	ReadOnly MinInt As Integer = 0
	Const MaxInt As Integer = 25

	Public Property WakeUpAt As Date Implements IAlarm.WakeUpAt
	' public DateTime WakeUpAt { get; set; }

	Public Sub New()
		' : this(DateTime.Now)
		Me.New(DateTime.Now)
	End Sub

	Public Sub New(ByVal time As Date)
		' : base()
		MyBase.New()
	End Sub

	Shared Sub New()
		' static constructor
	End Sub

	Protected Overrides Sub Finalize()
		' C# "destructor": ~MyColl() {}
		MyBase.Finalize()
	End Sub

	' Default value, Get and Set both Public
	Public Property Size As Integer = -1

	Private _name As String
	Public Property Name As String
		Get
			Return _name
		End Get
		Protected Set(ByVal value As String)
			_name = value
		End Set
	End Property

	' WriteOnly for setter only
	Public ReadOnly Property Name As String
		Get
			Return _name
		End Get
	End Property

	Shared Function Max(Of T As {IComparable, Structure})(ByVal ParamArray items As T()) As T
		' static T Max<T>(params T[] items) where T : IComparable, struct
		' It is possible to assign the return value to the method name instead of Return items.Max
		Max = items.Max
	End Function

	Protected Friend Function Yaye(Optional b As Boolean = True) As Boolean
		Static entered As Integer
		' Will increase with 1 each time the method is called
		entered += 1
		Return True
	End Function

	Private Function Yaye(Of Q As New)(ByRef b As String) As Q
		' private Q Yaye<Q>(ref string b) where Q : new()
		' VB.NET has no Out, only ByRef
		Return new Q()
	End Function

	Private Function YayeCaller() As Integer
		' Can pass a Property As ByRef
		Return Me.Yaye(Of Integer)(Name)
	End Function
End Class
```



### Linq, Lambdas and Anonymous Types


**Lambdas**:
```vb
Dim adder = Function(x) x + 1
' x => x + 1

Dim writer =
	Sub(time As TimeSpan)
		Console.WriteLine(time)
	End Sub
```

**Events**:
```vb
Public Class School
	Event Recess(ByVal time As TimeSpan)

	Sub Sit()
		' No null check required
		RaiseEvent Recess(TimeSpan.FromMinutes(15))
	End Sub
End Class

Shared Sub Main()
	Dim school As New School
	AddHandler school.Recess, writer
	' AddHandler school.Recess, AddressOf Me.Play
	school.Sit()
	RemoveHandler school.Recess, writer
End Sub
```


**Anonymous Types**:
```vb
Dim stu = New With {Key .Name = "Sue", .Gpa = 3.4}
' var stu2 = new {Name = "Sue", Gpa = 3.5};
```

C# doesn't have `Key` which is used for comparisons: `stu.Equals(stu2)`.  
VB.NET without the `Key` keyword and C# consider two anonymous types to be
equal if all their properties are equal.



**Linq**:
```vb
Public Class Person
	Property Name As String
	Property Age As Integer
End Class

Dim persons As New List(Of Person) From 
{
	New Person With {.Name = "Bob", .Age = 21},
	New Person With {.Name = "Alice", .Age = 25},
	New Person With {.Name = "Janice", .Age = 25}
}

Dim adults =
	From p In persons
	Where p.Age > 18
	Order By p.Name Descending
	Select New With {Key p.Name, .YearsOnEarth = p.Age}
	Take 5

' Do need _ for line continuations here
Dim lambdaAdults = persons _
	.Where(Function(p) p.Age > 18) _
	.OrderByDescending(Function(p) p.Name) _
	.Select(Function(p) New With {Key p.Name, .YearsOnEarth = p.Age}) _
	.Take(5)
```

Basic C# query syntax is exactly the same with all keywords in lowercase.
Except `Order By` which is `orderby x desc` in C#. VB.NET also adds some
extra functionality like `Take While`, `Select p Distinct`, `Skip` and
`Skip While`.

```vb
' Without projection, Select is optional
Dim peopleByAge =
	From p In persons
	Group By Age = p.Age
	Into People = Group, Count()

' TODO: VB.NET LINQ to add Group By, Join, Group Join, Aggregate and Let clauses
' I should probably add some additional stuff here with exact C# translations
' https://msdn.microsoft.com/en-us/library/bb763068.aspx
```




### Initializers

| VB.NET                                     | C#
|--------------------------------------------|--------------------------------------------|
| Dim nums = New Integer() {1, 2, 3}         | int[] nums = new[] {1, 2, 3};
| Dim nums() As Integer = {1, 2, 3}
| Dim names(4) As String ' 4 elements        | var names = new string[5];
| ReDim Preserve names(6) ' Preserve optional| Array.Resize(ref names, 7);
|                                            |                                            |
| Dim h = New Hero With {.Name = "Deadpool"} | var h = new Hero() {Name = "Deadpool"};
{: .table-code}


```vb
' VB.NET
Dim nums = New List(Of Integer) From {1, 2, 3}
Dim students As New Dictionary(Of Integer, String) From
{
	{123, "Bob"}, {456, "Alice"}
}
```

```c#
// C#
var nums = new List<int>() {1, 2, 3};
var students = new Dictionary<int, string>
{
	{123, "Bob"}, {456, "Alice"}
};
```



### Other

**Exception Handling**:
```vb
Try
Catch ex As ArgumentException When ex.ParamName = NameOf(Name)
	' No C# equivalent for When
	Throw
Catch ex As Exception
Finally
	Throw New Exception()
End Try
```

**Extension Methods**:
```vb
Module StringExtensions
	<Extension()>
	Public Function Vowels(ByVal s As String) As Integer
		' public static int Vowels(this string s) {}
		Return s.Count(Function(c) "aeiou".Contains(Char.ToLower(c)))
	End Function
End Module
```

**Using**:
```vb
Using r As StreamReader = File.OpenText("file.txt")
End Using
```

**Yield**:
```vb
Iterator Function Iterate() As IEnumerable(Of Integer)
	Yield 5
	Yield 7
End Function
```


* * *



Dangers of VB6 Legacy
---------------------
A new VB.NET project starts with `Option Strict Off` and `Option Explicit Off`. This is bad.  
... Unless you are a JavaScript developer, then you can just continue to do whatever you want!

```vb
' VB.NET
Dim name = "string"
name = 15
' name is now an integer or a string.. depending whether `Option Infer` is On or Off.
uhoh = 20 ' With `Option Explicit Off` this *will* compile.

// C# equivalent
object name = "string";
```

That's obviously not what one wants. Emulating C#'s `var name = "str"` behavior can be achieved
by turning `Option Strict On` and `Option Infer On` in the Project Properties Compile tab.

### Stuff that was imported from VB6

They are listed here, but you may skip this part and pretend it never happened.

```vb
' IIf always evaluates both branches. Use If() instead!
Dim result = IIf(someBool, "truthyCase", "falsyCase")

' Multiple statements on one line.
If True Then s = "1": s = "2"

' Who wouldn't want a good ol' GoTo in their code base
On Error GoTo MyErrorHandler
MyErrorHandler: Console.WriteLine(Err.Description)
```

### Microsoft.VisualBasic namespace

`Microsoft.VisualBasic.Strings`:  
If you want to forego Object Oriented, the whole set of VB6 string methods is available.  
```
UCase, Len, Replace, Format, Left, Mid, Trim, ...

' Mid can be called like this because Microsoft.VisualBasic.Strings is a Module
Dim s = Mid("testing", 2, 3)   ' est
```

`Microsoft.VisualBasic.Constants`:
- vbCancel = MsgBoxResult.Cancel
- vbNullString = Nothing
- vbLongDate = DateFormat.LongDate
- vbHidden = FileAttribute.Hidden
- VbFalse = TriState.False

`Microsoft.VisualBasic.DateAndTime`:
- DateAdd, DatePart, WeekDayName, ...

`Microsoft.VisualBasic.FileSystem`:  
ChDrive, SetAttr, Lock, Seek, CurDir, Input ...  
It's probably a good idea to steer away from these and stick to System.IO and Streams.

`Microsoft.VisualBasic.Interaction`:
- Beep: Ow yeah...
- SaveSetting/DeleteSetting: in the registry
- InputBox
- Environ: Get system environment variables
- Shell: Run an executable program

`Microsoft.VisualBasic.Devices`:  
- Audio: Play, [Stop]
- Clock: LocalTime, GmtTime
- Keyboard: ShiftKeyDown, NumLock, ...
- Computer: Clipboard, Ports, Mouse, Screen, ...
- ComputerInfo: OSFullName, TotalPhysicalMemory, AvailableVirtualMemory, ...
- Network: DownloadFile, UploadFile, Ping, NetworkAvailabilityChanged

`Microsoft.VisualBasic.Information`:  
- IsNumeric, IsDate, IsArray, L/UBound, RGB, ...

`Microsoft.VisualBasic.Logging`:  
- Write to the Application EventLog.



* * *



Available in C# only
--------------------
The only real blocker against VB.NET is unsafe and checked/unchecked code.
But if you really need to be working unsafely, perhaps you should consider not using .NET.

There is other C# only stuff that has already been covered (ex: Out parameters) and there is more like multi line comments
and `a++` but nothing serious that can't be worked around with some additional statements in VB.NET.

This somewhat changes with C# 7 features that have not been implemented for VB.NET:
- Out variables: `int.TryParse(s, out var i)`
- Pattern Matching: `o is int i`, switch
- `return ref x;`
- Tuples



* * *



Available in VB.NET only
------------------------
Many things have been covered already:
- More versatile switch
- Extra Linq query keywords (Take, Skip, Distinct, ...)
- `Key` keyword for anonymous types
- Like operator for simple string matching
- Static local variables
- Modules: Which can be (sort of) emulated with `static class` and static using statements.
- When blocks in exceptions
- Inline DateTime declarations
- Passing properties ByRef


**With**
```vb
With hero
	.Name = "SpamMan"
	.PowerLevel = 3
End With
```

**WithEvents and Handles**:
```vb
Dim WithEvents Button1 As New Button

Sub Button1_Click(sender As Object, e As EventArgs) Handles Button1.Click
End Sub
```


**My namespace**  
A whole bunch of convenience methods in `Microsoft.VisualBasic.ApplicationServices`.  
- `My.Application`: CommandLineArgs, Culture, ...
- `My.Computer`: Like Microsoft.VisualBasic.Devices
- `My.User`: CurrentPrincipal, IsAuthenticated, IsInRole
- `My.Settings`: Working with app.config AppSettings
- `My.Resources`: Easily get a resources as string or stream


**XML Literals**  
When you want to quickly build some Xml, VB.NET Xml literals can come in quite handy.

```vb
Dim xml As XElement = 
	<Root>
		<Title>Xml Literals</Title>
		<Items>
			<Item Type="Date"><%= Datetime.Now %></Item>
			<Item Type="String">Whoah</Item>
		</Items>
	 </Root>
```

You can even build Xml with dynamic node names and by meshing in some Linq.
It's probably more a thing for migration and test code and not so much for production code.

```vb
Dim persons = 
	<Persons>
		<%= From p In persons Select <Person><%= p.Name %></Person> %>
	</Persons>
```

Getting stuff out of XElement is also very nicely done:
```vb
Dim title = xml.<Root>.<Title>.Value
Dim nowNode = xml...<Item>.First()
Dim nowTypeAttribute = nowNode.@Type
Dim nowNodeText = nowNode.Value
```


* * *



Convertion troubles
-------------------
While there are some online converters, they are not at all up to date, or very smart.
- [Carlosag converter][converter-carlosag]
- [Telerik converter][converter-telerik] <- If you must, take this one

They do not provide much more than a starting point.

- Using an array accessor in VB.NET (`arr(0)` vs `arr[0]`) results in an incorrect translation
- "Newer" features, like lambdas are converted to dynamic, or not at all
- Use any VB.NET or C# only feature and they won't even make an attempt at converting it



* * *



Summary
-------
**So which is best**  
(just kidding:)

VB.NET used to be lacking many convenient C# features (using, yield, ...). While it has mostly catched up, 
C# proves to be a moving target.

Properties used to be a real pain in VB.NET. A one liner syntax is now available like
`Property Start As Date`. But by now C# already introduced property expression bodies
leaving VB.NET behind again.


For a long time VB.NET switch statement used to be more versatile but with the implementation of Pattern Matching
in C# this is no longer the case.

Lambda syntax like `o.Where(Function(x As Integer) x < 0)` isn't going to win any prizes when camped against
C#'s `o.Where((int x) => x < 0)`... But at least it's there :)

I'm probably a *little* biased towards C# because of the more succinct syntax and because the vast majority of
online examples are written in C# while the free convertion tools are severly lacking.


It's perhaps worth mentioning that VB.NET and Resharper are not on the best of terms.


Unless you really need some of the VB.NET or C# only features, choosing one over the other should probably
still be a team decision.




[converter-carlosag]: http://www.carlosag.net/tools/codetranslator/
[converter-telerik]: http://converter.telerik.com/

---
layout: post
title:  "Create Excels with C# and EPPlus: Import"
date:   2017-05-01 12:00:00 +0200
categories: dotnet
tags: [net,excel,tutorial]
series: epplus
extras:
  - githubproject: https://github.com/be-pongit/EPPlusTutorial
---

{% include toc title="Excel series: Part 3" icon="file-excel-o" %}

# IEnumerable objects
{: .hide-from-excerpt}

Exporting data to an Excel doesn't get easier than this

```c#
using (var package = new ExcelPackage())
{
	var sheet = package.Workbook.Worksheets.Add("IEnumerable");
	var data = new[]
	{
		new {Name = "A", Value = 1},
		new {Name = "B", Value = 2},
		new {Name = "C", Value = 3},
	};
	sheet.Cells["A1"].LoadFromCollection(data);
	sheet.Cells["A1"].SetHeaders("Name", "Value");
	package.SaveAs(new FileInfo(@""));
}
```

<!--more-->

Which would result in the following xlsx.

|   | A     | B        |
|---|-------|----------|
| 1 | Name  | Value    |
| 2 | A     | 1        |
| 3 | B     | 2        |
| 4 | C     | 3        |
{: .table-excel}

There are overloads for
- Writing headers or not
- `TableStyles`: Style the table in one of the predefined formats
- `BindingFlags` + `MemberInfo`: Allow you to export specific (private) fields

The pretty blue background for the header is done with an extension method.
The LoadFromXXX methods do not set any header styling themselves.

```c#
public static void SetHeaders(this ExcelRangeBase cell, params string[] headers)
{
	foreach (string text in headers)
	{
		cell.Value = text;
		cell.Style.Font.Bold = true;
		cell.Style.Fill.PatternType = ExcelFillStyle.Solid;
		cell.Style.Fill.BackgroundColor.SetColor(Color.DarkBlue);
		cell.Style.Font.Color.SetColor(Color.White);

		cell = cell.Offset(0, 1);
	}
}
```


**LoadFromArrays**:

```c#
using (var package = new ExcelPackage())
{
	var sheet = package.Workbook.Worksheets.Add("Arrays");
	var data = new[]
	{
		new[] {"A1", "B1", "C1"},
		new[] {"A2", "B2", "C3"},
	};
	sheet.Cells["A1"].LoadFromArrays(data);
	package.SaveAs(new FileInfo(@""));
}
```



# CSV file

```c#
using (var package = new ExcelPackage())
{
	var sheet = package.Workbook.Worksheets.Add("CSV");
	var file = new FileInfo("LoadFromText.csv");

	var format = new ExcelTextFormat()
	{
		Delimiter = ',',
		Culture = CultureInfo.InvariantCulture,

		// Escape character for values containing the Delimiter
		// ex: "A,Name",1 --> two cells, not three
		TextQualifier = '"'

		// Other properties
		// EOL, DataTypes, Encoding, SkipLinesBeginning/End
	};
	sheet.Cells["A1"].LoadFromText(file, format);
	package.SaveAs(new FileInfo(@""));
}
```



# DataReader &amp; DataTable

In case you ever need it, it's there :)

```c#
sheet.Cells["A1"].LoadFromDataTable(new DataTable(), true);
```

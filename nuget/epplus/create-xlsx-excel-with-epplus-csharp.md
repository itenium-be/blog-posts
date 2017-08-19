---
layout: post
title:  "Create Excels with C# and EPPlus: A tutorial"
date:   2017-04-26 22:00:00 +0200
categories: dotnet
tags: [net,excel,tutorial]
series: epplus
extras:
  - githubproject: https://github.com/be-pongit/EPPlusTutorial
---

{% include toc title="Excel series: Part 1" icon="file-excel-o" %}

Quick tutorial about creating xlsx Excels with
C# and the [EPPlus nuget package][epplus-nuget].

> EPPlus Excel addresses are not zero based: The first column is column 1!

Also, before writing any loops, you might want to check out the `LoadFromXXX` methods.
They are explained in depth in part 3 Import.

<!--more-->

The blog posts will only cover the most important functionality.
[More extensive code examples][github-project] can be found in the GitHub project.

Quickstart
==========
**Install**:  
```
Install-Package EPPlus
```

**Use**:  
```c#
using OfficeOpenXml;

using (var package = new ExcelPackage())
{
	ExcelWorksheet sheet = package.Workbook.Worksheets.Add("MySheet");

	// Setting & getting values
	ExcelRange firstCell = sheet.Cells[1, 1];
	firstCell.Value = "will it work?";
	sheet.Cells["A2"].Formula = "CONCATENATE(A1,\" ... Ofcourse it will!\")";
	Assert.That(firstCell.Text, Is.EqualTo("will it work?"));

	// Numbers
	var moneyCell = sheet.Cells["A3"];
	moneyCell.Style.Numberformat.Format = "$#,##0.00";
	moneyCell.Value = 15.25M;

	// Easily write any Enumerable to a sheet
	// In this case: All Excel functions implemented by EPPlus
	var funcs = package.Workbook.FormulaParserManager.GetImplementedFunctions()
		.Select(x => new {FunctionName = x.Key, TypeName = x.Value.GetType().FullName});
	sheet.Cells["A4"].LoadFromCollection(funcs, true);

	// Styling cells
	var someCells = sheet.Cells["A1,A4:B4"];
	someCells.Style.Font.Bold = true;
	someCells.Style.Font.Color.SetColor(Color.Ivory);
	someCells.Style.Fill.PatternType = ExcelFillStyle.Solid;
	someCells.Style.Fill.BackgroundColor.SetColor(Color.Navy);

	sheet.Cells.AutoFitColumns();
	package.SaveAs(new FileInfo(@"basicUsage.xslx"));
}
```

**Did you know:** It is a successor to ExcelPackage, hence the name.  
{: .notice--info}

**Boring text ahead**:  

While it's often not easy to find EPPlus examples online, the source code does
contain a whole bunch of extensive examples. Be sure to check them out.
<!-- TODO: After EPPlus moved from Codeplex, add a hyperlink to the examples project -->


Everything covered works with LibreOffice 5.0. EPPlus covers things that LibreOffice
doesn't seem to know how to handle. For example:

- Graphs
- LoadFromCollection `TableStyles`
- sheet.View.PageLayoutView = true
- sheet.Hidden = eWorkSheetHidden.VeryHidden
- ...

Examples
========

Opening &amp; Saving
--------------------

Open an existing Excel, or if the file does not exist,
create a new one when `package.Save()` is called.
```c#
using (var package = new ExcelPackage(new FileInfo(@"openingandsaving.xslx"), "optionalPassword"))
using (var basicUsageExcel = File.Open(@"basicUsage.xslx"), FileMode.Open))
{
	var sheet = package.Workbook.Worksheets.Add("sheet");
	sheet.Cells["D1"].Value = "Everything in the package will be overwritten";
	sheet.Cells["D2"].Value = "by the package.Load() below!!!";

	// Loads the worksheets from BasicUsage
	// (MySheet with A1 = will it work?)
	package.Load(basicUsageExcel);

	package.Save("optionalPassword");
	//package.SaveAs(FileInfo / Stream)
	//Byte[] p = package.GetAsByteArray();
}
```

Selecting cells
---------------
```c#
using (var package = new ExcelPackage())
{
	ExcelWorksheet sheet = package.Workbook.Worksheets.Add("MySheet");

	// One cell
	ExcelRange cellA2 = sheet.Cells["A2"];
	var alsoCellA2 = sheet.Cells[2, 1];
	Assert.That(cellA2.Address, Is.EqualTo("A2"));
	Assert.That(cellA2.Address, Is.EqualTo(alsoCellA2.Address));

	// Column from a cell
	// ExcelRange.Start is the top and left most cell
	Assert.That(cellA2.Start.Column, Is.EqualTo(1));
	// To really get the column: sheet.Column(1)

	// A range
	ExcelRange ranger = sheet.Cells["A2:C5"];
	var sameRanger = sheet.Cells[2, 1, 5, 3];
	Assert.That(ranger.Address, Is.EqualTo(sameRanger.Address));

	//sheet.Cells["A1,A4"] // Just A1 and A4
	//sheet.Cells["1:1"] // A row
	//sheet.Cells["A:B"] // Two columns

	// Linq
	var l = sheet.Cells["A1:A5"].Where(range => range.Comment != null);

	// Dimensions used
	Assert.That(sheet.Dimension, Is.Null);

	ranger.Value = "pushing";
	var usedDimensions = sheet.Dimension;
	Assert.That(usedDimensions.Address, Is.EqualTo(ranger.Address));

	// Offset: down 5 rows, right 10 columns
	var movedRanger = ranger.Offset(5, 10);
	Assert.That(movedRanger.Address, Is.EqualTo("K7:M10"));
	movedRanger.Value = "Moved";

	package.SaveAs(new FileInfo(@""));
}
```

Writing values
--------------
```c#
using (var package = new ExcelPackage())
{
	ExcelWorksheet sheet = package.Workbook.Worksheets.Add("MySheet");

	// Format as text
	sheet.Cells["A1"].Style.Numberformat.Format = "@";

	// Numbers
	sheet.SetValue("A1", "Numbers");
	Assert.That(sheet.GetValue<string>(1, 1), Is.EqualTo("Numbers"));
	sheet.Cells["B1"].Value = 15.32;
	sheet.Cells["B1"].Style.Numberformat.Format = "#,##0.00";
	Assert.That(sheet.Cells["B1"].Text, Is.EqualTo("15.32"));

	// Percentage
	sheet.Cells["C1"].Value = 0.5;
	sheet.Cells["C1"].Style.Numberformat.Format = "0%";
	Assert.That(sheet.Cells["C1"].Text, Is.EqualTo("50%"));

	// Money
	sheet.Cells["A2"].Value = "Moneyz";
	sheet.Cells["B2,D2"].Value = 15000.23D;
	sheet.Cells["C2,E2"].Value = -2000.50D;
	sheet.Cells["B2:C2"].Style.Numberformat.Format = "#,##0.00 [$€-813];[RED]-#,##0.00 [$€-813]";
	sheet.Cells["D2:E2"].Style.Numberformat.Format = "[$$-409]#,##0";

	// DateTime
	sheet.Cells["A3"].Value = "Timey Wimey";
	sheet.Cells["B3"].Style.Numberformat.Format = "yyyy-mm-dd";
	sheet.Cells["B3"].Formula = $"=DATE({DateTime.Now:yyyy,MM,dd})";
	sheet.Cells["C3"].Style.Numberformat.Format = DateTimeFormatInfo.CurrentInfo.FullDateTimePattern;
	sheet.Cells["C3"].Value = DateTime.Now;
	sheet.Cells["D3"].Style.Numberformat.Format = "dd/MM/yyyy HH:mm";
	sheet.Cells["D3"].Value = DateTime.Now;

	// A hyperlink (mailto: works also)
	sheet.Cells["C25"].Hyperlink = new Uri("http://pongit.be", UriKind.Absolute);
	sheet.Cells["C25"].Value = "Visit us";
	sheet.Cells["C25"].Style.Font.Color.SetColor(Color.Blue);
	sheet.Cells["C25"].Style.Font.UnderLine = true;

	sheet.Cells["Z1"].Clear();

	sheet.Cells.AutoFitColumns();
	package.SaveAs(new FileInfo(@""));
}
```

Styling cells
-------------
```c#
using (var package = new ExcelPackage())
{
	ExcelWorksheet sheet = package.Workbook.Worksheets.Add("Styling");
	sheet.TabColor = Color.Red;

	// Cells with style
	ExcelFont font = sheet.Cells["A1"].Style.Font;
	sheet.Cells["A1"].Value = "Bold and proud";
	sheet.Cells["A1"].Style.Font.Name = "Arial";
	font.Bold = true;
	font.Color.SetColor(Color.Green);
	// ExcelFont also has: Size, Italic, Underline, Strike, ...

	sheet.Cells["A3"].Style.Font.SetFromFont(new Font(new FontFamily("Arial"), 15, FontStyle.Strikeout));
	sheet.Cells["A3"].Value = "SetFromFont(Font)";

	// Borders need to be made
	sheet.Cells["A1:A2"].Style.Border.BorderAround(ExcelBorderStyle.Dotted);
	sheet.Cells[5, 5, 9, 8].Style.Border.BorderAround(ExcelBorderStyle.Dotted);

	// More style
	sheet.Cells[5, 5, 9, 8].Merge = true;
	sheet.Cells["D14"].Style.ShrinkToFit = true;
	sheet.Cells["D14"].Style.Font.Size = 24;
	sheet.Cells["D14"].Value = "Shrinking for fit";

	sheet.Cells["D15"].Style.WrapText = true;
	sheet.Cells["D15"].Value = "A wrap, yummy!";
	sheet.Cells["D16"].Value = "No wrap, ouch!";

	// Setting a background color requires setting the PatternType first
	sheet.Cells["F6:G8"].Style.Fill.PatternType = ExcelFillStyle.Solid;
	sheet.Cells["F6:G8"].Style.Fill.BackgroundColor.SetColor(Color.Red);

	// Horizontal Alignment needs a little workaround
	// http://stackoverflow.com/questions/34660560/epplus-isnt-honoring-excelhorizontalalignment-center-or-right
	var centerStyle = package.Workbook.Styles.CreateNamedStyle("Center");
	centerStyle.Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
	sheet.Cells["B5"].StyleName = "Center";
	sheet.Cells["B5"].Value = "I'm centered";

	// MIGHT NOT WORK (in LibreOffice):
	sheet.Cells["B6"].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
	sheet.Cells["B6"].Value = "I'm not centered? :(";

	package.SaveAs(new FileInfo(@""));
}
```

[epplus-nuget]: https://www.nuget.org/packages/EPPlus/
[github-project]: https://github.com/be-pongit/EPPlusTutorial

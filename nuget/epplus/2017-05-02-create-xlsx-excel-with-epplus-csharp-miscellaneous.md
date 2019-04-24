---
layout: post
author: Wouter Van Schandevijl
title:  "Create Excels with C# and EPPlus: Miscellaneous"
date:   2017-05-02 00:00:00 +0000
categories: dotnet
tags: [excel,tutorial]
img:
  url: excel-print.jpg
  origin: https://unsplash.com/photos/Tzm3Oyu_6sk
  desc: Photo by Bank Phrom
series: epplus
extras:
  - githubproject: https://github.com/itenium-be/EPPlusTutorial
toc:
    title: "Excel series: Part 4"
    icon: file-excel-o
package-versions:
  EPPlus: 4.1.0
---

# Miscellaneous features
{: .hide-from-excerpt}

EPPlus can do a whole lot more for you. This post covers some interesting stuff that didn't really fit anywhere else.

<!--more-->

```c#
// Set workbook properties like title, author, company, ...
OfficeProperties props = package.Workbook.Properties;

// Modify column props: AutoFit(), Hidden, ...
var colE = package.Workbook.Worksheets.First().Column(5);
```

## Printing
```c#
using (var package = new ExcelPackage())
{
	var sheet = package.Workbook.Worksheets.Add("Printing");
	sheet.Cells["A1"].Value = "Check the print preview (Ctrl+P)";

	var header = sheet.HeaderFooter.OddHeader;
	// &24: Font size
	// &U: Underlined
	// &"": Font name
	header.CenteredText = "&24&U&\"Arial,Regular Bold\" YourTitle";
	header.RightAlignedText = ExcelHeaderFooter.CurrentDate;
	header.LeftAlignedText = ExcelHeaderFooter.SheetName;

	ExcelHeaderFooterText footer = sheet.HeaderFooter.OddFooter;
	footer.RightAlignedText = $"Page {ExcelHeaderFooter.PageNumber} of {ExcelHeaderFooter.NumberOfPages}";
	footer.CenteredText = ExcelHeaderFooter.SheetName;
	footer.LeftAlignedText = ExcelHeaderFooter.FilePath + ExcelHeaderFooter.FileName;

	sheet.PrinterSettings.RepeatRows = sheet.Cells["1:2"];
	sheet.PrinterSettings.RepeatColumns = sheet.Cells["A:G"];

	sheet.View.PageLayoutView = true;

	package.SaveAs(new FileInfo(@""));
}
```


## Comments &amp; RichText

Each RichText.Add() returns a new object that takes over all styling from the textobject is was invoked on
and can then be styled separately.

```c#
using (var package = new ExcelPackage())
{
	var sheet = package.Workbook.Worksheets.Add("Rich Comments");

	ExcelComment comment = sheet.Cells["A1"].AddComment("Bold title:\r\n", "evil corp");
	comment.Font.Bold = true;
	comment.AutoFit = true;

	ExcelRichText rt = comment.RichText.Add("Unbolded subtext");
	rt.Bold = false;
	// Also rt.Color, FontName, Size, ...

	// A more extensive example can be found in Sample6.cs::AddComments of the official examples project
	// https://github.com/JanKallman/EPPlus/blob/master/SampleApp/Sample6.cs

	package.SaveAs(new FileInfo(@""));
}
```


## Converting indexes and ranges

```c#
[Test]
public void ConvertingIndexesAndAddresses()
{
	Assert.That(ExcelCellBase.GetAddress(1, 1), Is.EqualTo("A1"));
	Assert.That(ExcelCellBase.IsValidCellAddress("A5"), Is.True);

	Assert.That(ExcelCellBase.GetFullAddress("MySheet", "A1:A3"), Is.EqualTo("'MySheet'!A1:A3"));

	Assert.That(ExcelCellBase.TranslateToR1C1("AB23", 0, 0), Is.EqualTo("R[23]C[28]"));
	Assert.That(ExcelCellBase.TranslateFromR1C1("R23C28", 0, 0), Is.EqualTo("$AB$23"));
}
```

[The project code][github-project] also contains additional examples with

- Workbook & sheet protection from user editing
- A UnitTest to generate excel with a sample LoadFromXXX with a sheet for each TableStyles enum value.

[github-project]: https://github.com/itenium-be/EPPlusTutorial

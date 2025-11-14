---
layout: post
author: Wouter Van Schandevijl
title:  "Create Excels with C# and ClosedXML: Miscellaneous"
date:   2025-10-01
categories: dotnet
tags: [excel,tutorial]
img:
  url: closedxml-misc.png
  prompt: "spreadsheet cells with colorful speech bubbles hovering above them, playful vector illustration, modern flat design --ar 6:5"
  origin: Midjourney
bigimg:
  url: closedxml-misc-big.png
  prompt: "a renaissance woodcut illustration of a medieval scribe feeding scrolls into a strange mechanical printer, ink splattering, exaggerated comic style, high detail --ar 700:131"
  origin: Midjourney
series: closedxml
extras:
  - githubproject: https://github.com/itenium-be/ClosedXMLTutorial
    githubtext: "More extensive ClosedXML examples in the Github repo"
toc:
  title: "Excel series: Part 3"
  icon: file-excel-o
package-versions:
  - package: ClosedXML
    version: 0.105.0
socials:
  linkedin: "https://www.linkedin.com/posts/itenium_csharp-closedxml-dotnet-activity-7389979637579411456-rHyS"
  instagram: "https://www.instagram.com/p/DQeJ2TPjXVw/"
  facebook: "https://www.facebook.com/share/p/16XQJRAtcN/" 
  twitter: "https://x.com/itenium_be/status/1984213813445464457"
---


# Miscellaneous features
{: .hide-from-excerpt}

ClosedXML can do a whole lot more for you. This post covers some interesting stuff that didn't really fit anywhere else.

<!--more-->


```c#
// Set workbook properties like title, author, company, ...
XLWorkbookProperties props = workbook.Properties;

// Modify default settings for all new workbooks
workbook.RowHeight = 20; // & ColumnWidth
// DefaultShowGridLines, DefaultShowZeros, ...
```

## Printing

- [More advanced printing options](https://github.com/closedxml/closedxml/wiki#page-setup-print-options)
- [sheet.PageSetup.PaperSize options](https://github.com/closedxml/closedxml/wiki/Paper-Size-Lookup-Table)
- [Margins options](https://github.com/closedxml/closedxml/wiki/Margins-Tab) with `sheet.PageSetup.Margins`

```c#
using var workbook = new XLWorkbook();
var sheet = workbook.Worksheets.Add("Printing");

sheet.PageSetup.Header.Center.AddText("YourTitle", XLHFOccurrence.AllPages)
  .SetFontSize(24)
  .SetBold()
  .SetUnderline();
sheet.PageSetup.Header.Right.AddText(XLHFPredefinedText.Date, XLHFOccurrence.AllPages);
sheet.PageSetup.Header.Left.AddText(XLHFPredefinedText.SheetName, XLHFOccurrence.AllPages);

sheet.PageSetup.Footer.Right.AddText("Page ", XLHFOccurrence.AllPages);
sheet.PageSetup.Footer.Right.AddText(XLHFPredefinedText.PageNumber, XLHFOccurrence.AllPages);
sheet.PageSetup.Footer.Right.AddText(" of ", XLHFOccurrence.AllPages);
sheet.PageSetup.Footer.Right.AddText(XLHFPredefinedText.NumberOfPages, XLHFOccurrence.AllPages);

sheet.PageSetup.SetRowsToRepeatAtTop(1, 2);
sheet.PageSetup.SetColumnsToRepeatAtLeft(1, 7);

sheet.SheetView.View = XLSheetViewOptions.PageLayout;
```


## Comments

```c#
using var workbook = new XLWorkbook();
var sheet = workbook.Worksheets.Add("Comments");

var comment = sheet.Cell("D4").CreateComment();
comment.Author = "evil corp";
comment.AddSignature(); // Makes Author visible
comment.AddText("Bold title:\r\n").SetBold(); // or use .AddNewLine()
comment.AddText("Unbolded subtext").SetBold(false);

sheet.Cell("G4").CreateComment().AddText("Orientation = Vertical");
sheet.Cell("G4").GetComment().Style
  .Alignment.SetOrientation(XLDrawingTextOrientation.Vertical)
  .Alignment.SetAutomaticSize()
  .Margins.SetAll(0.25)
  .ColorsAndLines.SetFillColor(XLColor.RichCarmine)
  .ColorsAndLines.SetFillTransparency(0.25);

// Control positioning of the comment:
// sheet.Cell(G4").GetComment().Position

foreach (var cell in sheet.CellsUsed(XLCellsUsedOptions.Comments, c => c.HasComment))
{
  cell.GetComment().SetVisible();
}
```

## RichText

```c#
using var wb = new XLWorkbook();
var ws = wb.Worksheets.Add("Rich Text");

var cell1 = ws.Cell(1, 1).SetValue("The show must go on...");
cell1.Style.Font.FontColor = XLColor.Blue;
cell1.CreateRichText().Substring(4, 4)
  .SetFontColor(XLColor.Red)
  .SetFontName("Broadway");

ws.Cell(3, 1).CreateRichText()
  .AddText("Hello").SetFontColor(XLColor.Red)
  .AddText(" BIG ").SetFontColor(XLColor.Blue).SetBold()
  .AddText("World").SetFontColor(XLColor.Red);
```


# Migrating from EPPlus

| EPPlus                           | ClosedXML                     |
|----------------------------------|-------------------------------|
| **Basics**
| new ExcelPackage()               | new XLWorkbook()
| Workbook.Worksheets.Add("str")   | AddWorksheet("str")
|                                  |
| **With Sheet**                   |
| Cells[1, 1]                      | Cell(1, 1)
| Cell["A2"]                       | Cell("A2")
| Cells["A2:C5"]                   | Range("A2:C5")
| Cells["B2,D2"]                   | Cells("B2,D2") or Ranges("B2,D2")
| Dimension                        | LastRowUsed() & LastColumnUsed()
|                                  | Or RangeUsed()
| View.FreezePanes                 | SheetView.Freeze
|                                  | SheetView.FreezeRows & FreezeColumns
| View.ShowGridLines               | ShowGridLines
| View.ShowHeaders                 | ShowRowColHeaders
|                                  |
| **With Cell(s)**
| Address                          | Address.ToString()
| Start.Column                     | Address.ColumnNumber
| Formula                          | FormulaA1
| Text                             | GetString() & GetFormattedString()
| Hyperlink = new Uri()            | SetHyperlink(new XLHyperlink())
| Merge = true                     | Merge() / Unmerge()
|                                  |
| **Styling with Cell.Style**
| Font.Color.SetColor(Color.Ivory)          | Style.Font.SetFontColor(XLColor.Ivory)
|                                           | Style.Font.FontColor = XLColor.Ivory
| Fill.PatternType = ExcelFillStyle.Solid   | Fill.SetPatternType(XLFillPatternValues.Solid)
| Fill.BackgroundColor.SetColor(Color.Navy) | Fill.SetBackgroundColor(XLColor.Navy)
| Border.BorderAround()            | Border.OutsideBorder
| ShrinkToFit = true               | Alignment.ShrinkToFit = true
| WrapText = true                  | Alignment.WrapText = true
| HorizontalAlignment              | Alignment.Horizontal
|                                  |
| **Formulas**
| Formula                          | FormulaA1
| Calculate()                      | RecalculateAllFormulas()
|
| **Misc**                         |
| LoadFromCollection               | InsertTable / InsertData
| Cells.AutoFitColumns()           | ColumnsUsed().AdjustToContents()
{: .table-code}

# Other

Things not covered...

- [Sheet Protection](https://docs.closedxml.io/en/latest/features/protect.html)
- [AutoFilter](https://docs.closedxml.io/en/latest/features/autofilter.html)
- [Sorting](https://docs.closedxml.io/en/latest/features/sort.html)

## Tables

ClosedXML has good functionality and examples for creating tables:

- [In-depth info](https://docs.closedxml.io/en/latest/features/tables.html)
- [Example Inserting Tables](https://github.com/closedxml/closedxml/wiki/Inserting-Tables)
- [Example Inserting Data](https://github.com/closedxml/closedxml/wiki/Inserting-Data)
- [Pivot Tables](https://docs.closedxml.io/en/latest/features/pivot-tables.html)
  - [Transpose Ranges](https://github.com/closedxml/closedxml/wiki/Transpose-Ranges)

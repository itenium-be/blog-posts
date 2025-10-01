---
layout: post
author: Wouter Van Schandevijl
title:  "Create Excels with C# and ClosedXML: Formulas & DataValidation"
date:   2025-09-30
categories: dotnet
tags: [excel,tutorial]
img:
  url: closedxml-formulas.png
  prompt: "a glowing formula etched into glass, surrounded by checkmarks and red crosses symbolizing validation, modern minimalistic style, high contrast, elegant illustration"
  origin: Midjourney
bigimg:
  url: closedxml-formulas-big.png
  prompt: "ancient scrolls covered in mathematical symbols, a stern judge stamping VALID or INVALID on parchment, renaissance painting style, high detail --ar 700:131"
  origin: Midjourney
series: closedxml
extras:
  - githubproject: https://github.com/itenium-be/ClosedXMLTutorial
    githubtext: "More extensive ClosedXML examples in the Github repo"
interesting:
  - url: "https://support.microsoft.com/en-us/office/excel-functions-alphabetical-b3944572-255d-4efb-bb96-c6d90033e188"
    desc: "Alphabetical list of Excel functions"
toc:
  title: "ClosedXML Excel series: Part 2"
  icon: file-excel-o
package-versions:
  - package: ClosedXML
    version: 0.105.0
---

In case your users want to continue working with the Excels after generation.

Not a problem for ClosedXML. Instead of calculating values and writing them to
an excel, leverage the power of Excel formulas.

<!--more-->

# Formulas

[The project code][github-project] creates an Excel like this:

|   | A     | B        | C      | D          | E        | F         | G         | H       |
|---|-------|----------|--------|------------|----------|-----------|-----------|---------|
| 1 | Name  | Quantity | Price  | Base total | Discount | Total     | Special   | Payup   |
| 2 | Nails | 37       | €3.99  | =B2*C2     |          | =D2       |           | =F2 * 0.8
| 3 | Hammer| 5        | €12.10 | =B3*C3     | 10%      | =D3 * 0.9 |           | =F3 * 0.8
| 4 | Saw   | 12       | €15.37 | =B4*C4     |          | =D4       |           | =F4 * 0.8
| 5 | #COUNT|          |        | #SUBTOTAL  |          | #SUBTOTAL | 20%       | #TOTAL
{: .table-excel}

The formulas below show how to set the #FORMULAS.

When setting a range with `FormulaA1`, it will not adjust columns/rows
accordingly, you have to use `FormulaR1C1`.

```c#
using var workbook = new XLWorkbook();
var sheet = workbook.Worksheets.Add("Formula");

// Starting = is optional
sheet.Cell("A5").FormulaA1 = "=COUNTA(A2:A4)";

// Total column
sheet.Range("D2:D4").FormulaR1C1 = "RC[-2]*RC[-1]";
Assert.That(sheet.Cell("D2").FormulaR1C1, Is.EqualTo("RC[-2]*RC[-1]"));
Assert.That(sheet.Cell("D4").FormulaR1C1, Is.EqualTo("RC[-2]*RC[-1]"));

// Total - discount column
// Calculate formulas before they are available in the sheet
// (Opening an Excel with Office will do this automatically)
// Cell.Value has a performance hit because it will calculate formulas.
sheet.Range("F2:F4").FormulaR1C1 = "IF(ISBLANK(RC[-1]),RC[-2],RC[-2]*(1-RC[-1]))";
Assert.That(sheet.Cell("F2").CachedValue.ToString(), Is.Empty);
workbook.RecalculateAllFormulas();
Assert.That(sheet.Cell("F2").CachedValue.ToString(), Is.Not.Empty);

// Total row
sheet.Cell("D5").FormulaR1C1 = "SUBTOTAL(9,R[-3]C:R[-1]C)"; // total
Assert.That(sheet.Cell("D5").FormulaA1, Is.EqualTo("SUBTOTAL(9,D2:D4)"));
sheet.Cell("F5").FormulaR1C1 = "SUBTOTAL(9,R[-3]C:R[-1]C)"; // total - discount
Assert.That(sheet.Cell("F5").FormulaA1, Is.EqualTo("SUBTOTAL(9,F2:F4)"));

workbook.RecalculateAllFormulas();
sheet.Range("H2:H5").FormulaR1C1 = "RC[-2]*(1-R5C7)"; // R5C7 is G5

// Recalculate all dirty formulas before saving
workbook.SaveAs("file.xlsx", new SaveOptions() {EvaluateFormulasBeforeSaving = true});
```

## Support

- [Supported Functions](https://github.com/closedxml/closedxml/wiki/Evaluating-Formulas#supported-functions)
- [Graphical representation of function evaluation](https://docs.closedxml.io/en/latest/concepts/function-evaluation.html#id1)


## SUBTOTAL

SUBTOTAL(9,...), as opposed to using SUM directly, will not include other subtotals in their calculation.

```vba
SUBTOTAL(FUNC_NUM,ref1,[ref2],...)
```

Add 100 to FUNC_NUM to ignore manually hidden rows.
Filtered-out rows are always excluded.

| FUNC_NUM | Function name | Desc
|----------|---------------|-----
| 1        | AVERAGE
| 2        | COUNT         | Counts how many numbers
| 3        | COUNTA        | Counts how many values
| 4        | MAX
| 5        | MIN
| 6        | PRODUCT
| 7        | STDEV         | Estimates standard deviation based on a sample
| 8        | STDEVP        | Calculates standard deviation based on the entire population
| 9        | SUM
| 10       | VAR           | Estimates variance based on a sample
{: .table-code}


# DataValidation

## Dropdownlists

```c#
using var workbook = new XLWorkbook();
var sheet = workbook.Worksheets.Add("Validation");

var validation = sheet.Cell("C7").CreateDataValidation();
// ATTN: The list must start and end with a double quote!
validation.List("\"Apples,Oranges,Lemons\"");

// Also possible to load from another sheet:
validation.List("OtherSheet!A1:A4");

validation.ErrorStyle = XLErrorStyle.Stop;
validation.ErrorTitle = "Invalid Selection";
validation.ErrorMessage = "We only have those available :(";
validation.ShowErrorMessage = true;

validation.InputTitle = "Choose your juice";
validation.InputMessage = "Apples, oranges or lemons?";
validation.ShowInputMessage = true;

validation.IgnoreBlanks = true;
```

## Number & DateTime validation

```c#
using var workbook = new XLWorkbook();
var sheet = workbook.Worksheets.Add("intsAndSuch");

// Integer/Decimal validation
var validation = sheet.Cell("A2").CreateDataValidation();
validation.WholeNumber.Between(1, 5);
// validation.Decimal.Between(1, 5);

// DateTime validation
var dateTimeValidation = sheet.Cell("B2").CreateDataValidation();
dateTimeValidation.Date.GreaterThan(DateTime.Now.Date);

// ATTN: While CreateDataValidation().Time does exist,
//       it was crashing for me when opening the Excel
```

[github-project]: https://github.com/itenium-be/ClosedXMLTutorial

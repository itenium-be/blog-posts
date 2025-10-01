---
layout: post
author: Wouter Van Schandevijl
title:  "Excel formulas cheat sheet"
date:   2025-10-02
desc: >
  Supported Excel formula functions in ClosedXML.
img:
  url: closedxml-functions2.png
  prompt: "a wizard casting glowing spells in the shape of formulas IF, DATE, COUNTIF, floating symbols swirling around, fantasy illustration, dramatic lighting --ar 6:5"
  origin: Midjourney
bigimg:
  url: closedxml-functions-big.png
  prompt: "an illuminated manuscript showing magical runes shaped like Excel formulas, monks scribbling COUNTIF and CONCATENATE in the margins, high detail, satirical medieval art --ar 700:131"
  origin: Midjourney
categories: dotnet
tags: [excel,cheat-sheet]
series: closedxml
extras:
  - githubproject: https://github.com/itenium-be/ClosedXMLTutorial
toc:
  title: "Excel series: Part 4"
  icon: file-excel-o
interesting:
  - desc: "Official documentation: list of all Excel functions"
    url: https://support.office.com/en-us/article/Excel-functions-alphabetical-b3944572-255d-4efb-bb96-c6d90033e188
---


Companion to the ClosedXML series, specifically to Part 2: Formulas.  
Covering the ClosedXML syntax and implemented functions.

See the [ClosedXML example code](https://github.com/itenium-be/ClosedXMLTutorial)
for  of all the functions as UnitTests.


<!--more-->

# Basics

Add a formula with:  

```c#
using var workbook = new XLWorkbook();
var sheet = workbook.Worksheets.Add("StringManipulation");
sheet.Cell("A1").FormulaA1 = "A$5";
sheet.Cell("D5").FormulaR1C1 = "RC[-2]*RC[-1]";
```

A few ClosedXML specific gotchas:  
- Formulas may start with =
- Use English function names
- Use , as function parameter separator
- A function that is [not implemented](https://docs.closedxml.io/en/latest/features/functions.html) can be set but the value cannot be read

# String manipulation

All string indexes start from 1.

| Function                                 | C#                                        | Remarks
|------------------------------------------|-------------------------------------------------
| LEN(str)                                 | str.Length
| TRIM(str)                                | str.Trim
| LOWER(str)                               | str.ToLower
| UPPER(str)                               | str.ToUpper
| PROPER(str)                              | To Title Case - Like This
| LEFT(str, cnt)                           | str.Substring(0, cnt)                     | Also: RIGHT
| MID(str, start, length)                  | str.Substring(start - 1, length)
| REPLACE(str, startIndex, length, newStr) | Use SUBSTITUTE instead
| SUBSTITUTE(str, "the", "a")              | str.Replace("the", "a")
| REPT(str, number)                        | string.Concat(Enumerable.Repeat(str, number))
| CONCATENATE(str1, str2, ...)             | str1 + str2
| str1 & str2                              | str1 + str2
| FIND(str, needle)                        | str.IndexOf(needle) + 1                   | Case sensitive
| SEARCH(str, needle)                      | Case insensitive FIND
| ISTEXT
{: .table-code}


# Numbers & Math

| Function                                        | Meaning                                   
|-------------------------------------------------|-------------------------------------------
| VALUE(str)                                      | decimal.Parse(str)
| ROUNDDOWN(number, num_digits)                   | Or TRUNC
| INT(number)                                     | Math.Floor
| ROUNDUP(number, num_digits)                     | 
| ROUND(number, num_digits)                       | 
| FLOOR(number, significance)                     | The closest multiple of "significance"
| CEILING(number, significance)                   | The closest multiple of "significance"
| ISNUMBER, ISEVEN, ISODD
| MAX, MIN
| COUNT(range)                                    | Counts all numeric cell values
| COUNTA(range)                                   | Counts all non empty cell values
| COUNTBLANK(range)
| COUNTIF(range, criteria)
| COUNTIFS(range, criteria, range2, crit2)        | All the ranges must be the same size.
|                                                 | Counts if all the conditions are true
| SUM, SUMIF
| SUMIFS(sum_range, range1, crit1, range2, crit2) | All the ranges must be the same size.
| AVERAGE(range)
| AVERAGEIF(range, criteria)                      | Not implemented
| AVERAGEIFS(avg_range, range1, crit1, ...)       | Not implemented
{: .table-code}

## CountIf, SumIf, AverageIf

Criteria possibilities for these xxxIF functions:

- A literal (ex: 15 or "value")
- Another cell or function result
- `">=10"`, `"=0"`, ...
- `"<>"&A1`: not equal to A1
- `"gr?y"`: single letter wildcard
- `"cat*"`: 0..x wildcard


## Real mathy

| Function                                 | Meaning                                   
|------------------------------------------|-------------------------------------------
| ABS(number)                              | Absolute value
| SIGN(number)                             | Returns 1 or -1
| PRODUCT(range...)                        | Returns arg1 * arg2 * ...
| POWER(base, exponent)                    | Or base^exp. Also: SQRT
| MOD(dividend, divisor)                   | Modulo. Also: QUOTIENT
| RAND()                                   | Between 0 and 1
| RANDBETWEEN(low, high)                   | Both params inclusive
| LARGE(range, xth)                        | Returns xth largest number
| SMALL(range, xth)                        | Returns xth smallest number
{: .table-code}

And then a whole bunch like:  
- PI, SIN, COS, ASIN, ASINH, TAN, ATAN, ...
- EXP, LOG, LOG10, LN
- MEDIAN, STDEV, RANK, VAR


## SUBTOTAL

SUBTOTAL(9,...), as apposed to using SUM directly, will not include other subtotals in the calculation.

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


# Date & Time

| Function                                    | C# / Info                                 
|---------------------------------------------|-------------------------------------------
| DATE(year, month, day)                      | new DateTime(year, month, day)
| TIME(hour, min, sec)
| TODAY                                       | DateTime.Today
| NOW                                         | DateTime.Now
| YEAR, MONTH, DAY
| HOUR, MINUTE, SECOND
| WEEKNUM                                     | Week of year. Also: ISOWEEKNUM
| WEEKDAY                                     | Day of week index (sunday=0)
| 
| Calculations:  
| TODAY() - 2                                 | DateTime.Now.Date.Subtract(TimeSpan.FromDays(2))
| NOW() + "2:00"                              | DateTime.Now.Add(TimeSpan.FromHours(2))
| DAYS360(date1, date2)                       | Difference in days
| YEARFRAC(date1, date2)                      | Difference in years (including fractional part)
| EDATE(date, nrOfMonths)                     | Add nrOfMonths to date
| EOMONTHS(date, 0)                           | Returns the last day of the date month
| EOMONTHS(date, -2)                          | Returns the last day of the month of (date - 2 months)
| WORKDAY(date, workDaysToAdd, holidaysRange) | Add working days to date. holidaysRange is optional
| DATEDIF(date1, date2, unit)                 | Unit: Y, M, D, MD (Ignore YM), YM (Ignore Y), YD (Ignore Y)
| NETWORKDAYS(date1, date2, [holidays])       | Working days between dates (excluding optional holidays)
{: .table-code}


# Boolean logic

| Function                                 | Meaning                                         
|------------------------------------------|-------------------------------------------------
| ISBLANK
| ISTEXT, ISNONTEXT
| T(A1)                                    | typeof A1 === "string" ? A1 : ""
| EXACT(strA, strB)                        | strA == strB. Case sensitive.
| ISNUMBER, ISEVEN, ISODD
| ISLOGICAL                                | Is a boolean?
| TRUE(), FALSE()                          | True/False values
{: .table-code}

## Or, And, Not

```vb
' Check if cell is blank
IF(OR(ISBLANK(A1), TRIM(A1)=""), 1, 0)

' Check if cell is either value
IF(OR(A1="value", A1="value2"), "value1-2", "other")
```

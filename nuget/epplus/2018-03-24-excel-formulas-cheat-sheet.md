---
layout: post
author: Wouter Van Schandevijl
title:  "Excels formulas cheat sheet"
date:   2018-03-28 20:00:00 +0200
img: epplus-excel.png
categories: dotnet
tags: [net,excel,tutorial]
series: epplus
extras:
  - githubproject: https://github.com/itenium-be/EPPlusTutorial
toc:
    title: "Excel series: Part 5"
    icon: file-excel-o
interesting:
    - https://support.office.com/en-us/article/Excel-functions-alphabetical-b3944572-255d-4efb-bb96-c6d90033e188
---

Companion to the EPPlus series, specifically to Part 2: Formulas.  
Covering the EPPlus syntax and implemented functions.

<!--more-->

# Basics

Add a formula with:  

```
var package = new ExcelPackage();
var sheet = package.Workbook.Worksheets.Add("Formulas")
sheet.Cells["A1"].Formula = "A$5";
sheet.Cells["D5"].FormulaR1C1 = "RC[-2]*RC[-1]";
```

A few EPPlus specific gotchas:  
- Do not start .Formula with =
- Use English function names
- Use , as function parameter separator



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
{: .table-code}


# Numbers & Math

| Function                                 | Meaning                                   
|------------------------------------------|-------------------------------------------
| VALUE(str)                               | decimal.Parse(str)
| FLOOR(number, significance)              | Or INT, ROUNDDOWN, TRUNC
| CEILING(number, significance)            | Or ROUNDUP
| ROUND(number, significance)
| ISNUMBER, ISEVEN, ISODD
| MAX, MIN
| COUNT(range)                             | Counts all numeric cell values
| COUNTA(range)                            | Counts all non empty cell values
| COUNTBLANK(range)
| COUNTIF(range, criteria)
| COUNTIFS(range, criteria, range2, crit2) | 
| SUM, SUMIF, SUMIFS
| AVERAGE, AVERAGEIF, AVERAGEIFS
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
| MOD(divident, divisor)                   | Modulo. Also: QUOTIENT
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

| Function                                 | C#                                        
|------------------------------------------|-------------------------------------------
| DATE(year, month, day)                   | new DateTime(year, month, day)
| TODAY                                    | DateTime.Now.Date
| NOW                                      | DateTime.Now
| DATE(date)                               | date.Date
| Also: MONTH, YEAR, TIME, HOUR, MINUTE, SECOND
| WEEKNUM                                  | Week of year. Also: ISOWEEKNUM
| WEEKDAY                                  | Day of week index (sunday=0)
| 
| Calculations:  
| TODAY() - 2                              | DateTime.Now.Date.Subtract(TimeSpan.FromDays(2))
| NOW() + "2:00"                           | DateTime.Now.Add(TimeSpan.FromHours(2))
| DAYS360(date1, date2)                    | Difference in days
| YEARFRAC(date1, date2)                   | Difference in years (including fractional part)
| EDATE(date, nrOfMonths)                  | Add nrOfMonths to date
| EOMONTHS(date, 0)                        | Returns the last day of the date month
| EOMONTHS(date, -2)                       | Returns the last day of the month of (date - 2 months)
| WORKDAY(date, workDaysToAdd, holidaysRange) | Add working days to date. holidaysRange is optional
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

```
' Check if cell is blank
IF(OR(ISBLANK(A1), TRIM(A1)=""), 1, 0)

' Check if cell is either value
IF(OR(A1="value", A1="value2"), "value1-2", "other")
```

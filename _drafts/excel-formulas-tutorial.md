---
layout: post
title:  "Excels formulas cheat sheet"
date:   2017-04-26 22:00:00 +0200
categories: productivity
tags: [excel]
---

Companion to the EPPlus series.
Specifically to Part 2: Formulas.

<!--more-->

Supported by EPPlus:  
http://epplus.codeplex.com/wikipage?title=Supported%20Functions&referringTitle=Documentation

Official docs:  
https://support.office.com/en-us/article/Excel-functions-alphabetical-b3944572-255d-4efb-bb96-c6d90033e188?ui=en-US&rs=en-US&ad=US#bm16

worksheet.Cells["A1"].Formula="CONCATENATE(\"string1_\",\"test\")";
TRIM, LRN ... and other string functions
=IF(OR(ISBLANK(A1),TRIM(A1)=""),1,0)

Also: CEILING, ROUNDING, FLOORING and other numeric functions

SUBTOTAL --> This is copied from Part 2: Formulas... IF we do this, just add a reference to this there... :)
--------
SUBTOTAL(9,...), as apposed to using SUM directly, will not include other subtotals in their calculation.

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



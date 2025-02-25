---
layout: post
author: Wouter Van Schandevijl
title:  "EPPlus: No longer free to use in a commercial setting"
date:   2023-03-10 00:00:00 +0200
categories: dotnet
tags: [excel]
img:
  url: epplus-commercial.png
bigimg:
  url: epplus-commercial-big.png
  prompt: "excel fork"
  origin: Midjourney
series: epplus
toc:
  title: "Excel series: EPPlus Commercial"
  icon: ban
last_modified_at: 2025-02-25 00:00:00
updates:
  - date: 2025-02-25
    desc: "Added EPPlus forks"
package-versions:
  - package: EPPlus
    version: 4.5.3.3
  - package: EPPlus-Commercial
    version: 5.0.3
---


**Version 4.5.3.3 is the last version of EPPlus you can use without a license for commercial use.**  
{: .notice--danger}

<!--more-->

# EPPlus Repositories


{% include github-stars.html url="JanKallman/EPPlus" desc="The old and free one" %}
{% include github-stars.html url="EPPlusSoftware/EPPlus" desc="The new and not so free one" %}

## Forks

EPPlus is a pretty solid tool and you probably don't need any of the additional
fluff they're adding in the now paid version. Use these to circumvent the official
EPPlus misleading "Deprecated" notice.

{% include github-stars.html url="rimland/EPPlus" desc="Most stars but no activity" %}
{% include github-stars.html url="visualon/epplus" desc="Actively maintained (ex .NET 8 support) but no new features" %}


# Alternatives

If you prefer something that is more actively maintained/developed:

{% include github-stars.html url="ClosedXML/ClosedXML" desc=".NET library for reading, manipulating and writing Excel files" %}
{% include github-stars.html url="nissl-lab/npoi" desc=".NET library to read/write Office formats" %}
{% include github-stars.html url="mini-software/MiniExcel" desc="Easy Excel .NET helper to import/export/template spreadsheets" %}
{% include github-stars.html url="mganss/ExcelMapper" desc="An Excel to object mapper." %}



## If you just need to read

{% include github-stars.html url="ExcelDataReader/ExcelDataReader" desc="Lightweight C# library for reading Excel files" %}

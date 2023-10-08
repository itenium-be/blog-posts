---
layout: post
author: Wouter Van Schandevijl
title: "Hacking Tool: sqlmap"
subTitle: "Database takeover tool"
date: 2023-10-03
desc: >
  Automated discovery of SQL injection vulnerabilities.
  sqlmap works for all commonly used databases, and then some.
bigimg:
  url: hacking-sqlmap-big.png
  desc: ""
  origin: Midjourney
  prompt: "sql injection vulnerability exploited"
img:
  url: hacking-sqlmap.png
  desc: ""
  origin: Midjourney
  title: ""
  prompt: "a seringe being injected into a server rack"
categories: security
tags: [tutorial,hacking]
series: security-bootcamp
toc:
  title: sqlmap
  icon: database
---


[sqlmap](https://sqlmap.org/) is a CLI tool for automated discovery of SQL injection vulnerabilities in web applications.


# What
{: .hide-from-excerpt}

{% include github-stars.html url="sqlmapproject/sqlmap" desc="Automatic SQL injection and database takeover tool" %}

<!--more-->

It has some... interesting features:  
- Enumerate users, roles, databases, ... and dump all rows
- Password hash recognition and attempt cracking with a dictionary attack
- Download and upload any file
- Search for tables, columns, execute arbitrary SQL, ...
- Database process' user privilege escalation
- Setup an interactive command prompt




# How

```sh
sqlmap -u 'url' --dump
```

Flags used:

- `-u 'url'`: Target URL (e.g. "http://site.com/vuln.php?id=1")
- `--dump`: Dump DBMS database table entries

Other interesting flags:

- `-f`: Perform an extensive DBMS version fingerprint
- `--wizard`: Simple wizard interface for beginner users
- `-v VERBOSE`: Verbosity level: 0-6 (default 1)
- `--sql-query=QUERY`: SQL statement to be executed
- `--sql-shell`: Prompt for an interactive SQL shell
- `--search`: Search column(s), table(s) and/or database name(s)
- `--technique=TECH`: SQL injection techniques to use (default "BEUSTQ")
    - `B`: boolean-based blind (Add `OR` SQL. ex: `OR 1=1` to get all rows)
    - `E`: error-based (force an error with interesting error message, ex: `cur_db() as numeric`)
    - `U`: UNION query-based (Start with `UNION` and add an extra query)
    - `S`: stacked queries (Use `;` and add a whole new query. End with `--` so the rest of the query is commented out)
    - `T`: time-based blind (if error messages are not displayed, use time to get a true/false answer `IF(version()=5.3, sleep(5), 'false')`)
    - `Q`: inline queries (Inject subqueries)
- `--udf-inject`: Inject custom user-defined functions
- Operation System
    - `--reg-read`: Read a Windows registry key value (also `--reg-add` & `--reg-del`)
    - `--os-cmd=OSCMD`: Execute an operating system command
    - `--os-shell`: Prompt for an interactive operating system shell


## Docker


```sh
docker run --rm paoloo/sqlmap --help

mkdir sqlmap-data
docker run --rm -it -v ./sqlmap-data:/root/.sqlmap/ paoloo/sqlmap --url "url" --dump
```


# When

During itenium's Security Bootcamp `sqlmap` can be used to retrieve
a flag hidden in a database by attacking a vulnerable web page.

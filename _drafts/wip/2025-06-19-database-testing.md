---
layout: post
author: Wouter Van Schandevijl
title: "Database Testing with EF"
subTitle: "Let's explore your options"
date: 2025-11-26
desc: >
  After seeing a session about this on Techorama,
  and presenting this during our tech session at
  itenium on Test Containers, the blog post!
bigimg:
  url: -big.png
  prompt: ""
  origin: Midjourney
img:
  url: .png
  origin: Midjourney
  prompt: ""
categories: dotnet
tags: [sql,testing]
extras:
  - githubproject: https://github.com/itenium-be/DbContext-Testing
    githubtext: "The accompanying code on GitHub"
toc:
  title: Database Tests
  icon: dot-circle-o
---

Back in May 

Tuesday 27 May 2025
16:00 - 17:00
Room 4
Intermediate

https://techorama.be/agenda/session/databases-ef-and-testing-your-applications-making-sense-of-all-the-confusion/

By Shay Rojansky


Databases, EF, and testing your applications - making sense of all the confusion
Having a proper testing strategy for applications using a database is surprisingly hard, and there's tons of confusion and differing strong opinions on the matter. Once you use EF, things can become even more confusing: should you use the InMemory provider, or SQLite to speed up your tests? Or should you test against your production database system (e.g. SQL Server)? What about test isolation, and how does the repository pattern fit into this? In this session, we'll explore all these questions and chart out the possible options.

<!--more-->


- Hoe omgaan met databases?
--> Repository Pattern --> No SQL dialect issues etc but extra layer in the code + you still need tests to check if database interaction actually works
--> DbSet Mocks vs InMemory Database vs SQLite --> Case sensitivity (SqlServer case insensitive vs SQLite case sensitive) + SQL Dialects + Cannot use actual SQL for InMemory/Mocks
--> Actual database --> With testcontainers this can still be pretty fast (setup docker container once, run migrations once, create a transaction so that it is rolled back automatically after each test)
--> Maybe do a little writeup? 😀 







<!--block1-->

---
layout: post
author: Wouter Van Schandevijl
title:  ECMAScript version 2015 (6)
date:   2018-10-09 12:00:00 +0200
categories: javascript
tags: [tutorial]
desc: >
  
img: .png
interesting:
  - desc: Kangax Compat Tables
    url: http://kangax.github.io/compat-table/
  - desc: Overview and Comparison of all new features in ES2015 (ES6)
    url: http://es6-features.org/
  - desc: 
    url: 
series: ecmascript-versions
toc:
    title: ECMAScript2015 (ES6)
    icon: eye-javascript
---


<!--more-->

# Scoping
let/const



# Functions
default parameter values

rest parameter
function f (x, y, ...a) {
spread operator
f(1, 2, ...[3, 4, 5])

## Arrow-Functions



# Template String


# Properties

property shorthand
obj = { x, y }
obj = { x: x, y: y };

Computed Property Names

Method Properties
obj = { foo() {} }
obj = { foo: foo() {} }


# Destructuring

var [one, , three] = [1, 2, 3]
[ b, a ] = [ a, b ]

const { r, g, [b1, b2, b3] } = (() => ({r: 0, g: 0, b: '255'}))();


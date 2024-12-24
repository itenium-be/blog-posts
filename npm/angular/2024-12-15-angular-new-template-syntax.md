---
layout: post
author: Wouter Van Schandevijl
title: "The new Angular template syntax"
subTitle: "Shorter & Sweeter"
date: 2024-12-15
desc: >
  I don't think anyone ever liked the `*ngIf`,
  nor all the typing that came with it.
  <br><br>
  Now it's finally become much shorter, more versatile,
  and with standalone being the new default, also no
  longer needed to be explicitly imported!
bigimg:
  url: angular-template-syntax-big.png
  prompt: "angular template"
  origin: Midjourney
img:
  url: angular-template-syntax.png
  origin: Midjourney
  prompt: "template"
categories: javascript
tags: [tutorial,angular]
extras:
  - githubproject: https://github.com/itenium-be/Angular-Days
    githubtext: "See src/app/new-template for side-by-side comparison"
package-versions:
  - package: angular
    version: 19
toc:
  title: Template Syntax
  icon: icon-angular
socials:
  linkedin: "https://www.linkedin.com/posts/itenium_itenium-keeponlearning-itdevelopment-activity-7275827773707829248-edGh"
  facebook: "https://www.facebook.com/share/p/186FgZJHrW/"
  twitter: "https://x.com/itenium_be/status/1870061737682018670"
---

Compared to other SPA frameworks, Angular really made it quite
cumbersome to do simple things like an if/else to show/hide
certain UI parts.

The new syntax is so much more succint, it gets rid of excess
html tags, is easier to remember and it doesn't need to be
added to the `imports` array of a standalone component.

<!--more-->

# @if

## Old Syntax

Need to import `NgIf` from `@angular/common`.

```html
<span *ngIf="boolVar; else boolFalse">
  TRUE
</span>
<ng-template #boolFalse>
  FALSE
</ng-template>
```

## New Syntax

```html
@if(boolVar) {
  TRUE
} @else {
  FALSE
}
```

# @for

## Old Syntax

Need to import `NgFor` from `@angular/common`. 

```html
<span *ngFor="let el of elements; index as i; first as isFirst">
  {% raw %}{{ i }}: {{ el.desc }} (first={{isFirst}}){% endraw %}
</span>
```

Available "special" variables:
- index & count
- odd & even
- first & last

Adding `trackBy: trackElFn` is optional
and is only needed when you are running into performace issues.

```ts
trackElFn(index: number, el: T) {
  return el.id;
}
```


## New Syntax

The track is mandatory in the new syntax.

```html
@for(el of elements; track el.id; let i = $index; let isFirst = $first) {
  {% raw %}{{ i }}: {{ el.desc }} (first={{isFirst}}){% endraw %}
}
```

# @switch

## Old Syntax

Need to import `NgSwitch`, `NgSwitchCase` and `NgSwitchDefault` from `@angular/common`.

```html
<div [ngSwitch]="aNumber">
  <span *ngSwitchCase="1">ONE</span>
  <span *ngSwitchCase="2">TWO</span>
  <span *ngSwitchDefault>OTHER</span>
</div>
```

## New Syntax

```html
@switch(randomNumber) {
  @case(1) {ONE}
  @case(2) {TWO}
  @default {OTHER}
}
```

# @let

## Old Syntax

This was previously not possible out of the box but the
same could be achieved with an `ngIf`:

```html
<span *ngIf="randomObs$ | async as randomNr">
  Nr = {% raw %}{{ randomNr }}{% endraw %}
</span>
```


## New Syntax

Don't forget the ending `;`!

```html
@let randomNr = randomObs$ | async;
Nr = {% raw %}{{ randomNr }}{% endraw %}
```

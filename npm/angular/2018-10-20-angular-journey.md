---
layout: post
author: Steven Robijns
title:  "Angular Road Trip"
date:   2018-10-20 00:00:00
desc: >
    2+ years of Angular: The good, the bad and the ugly
img: angular.png
imgdesc:
categories: javascript
tags: [javascript,typescript,angular]
extras:
interesting:
  - url: https://angular.io/
    desc: Angular
  - url: https://cli.angular.io/
    desc: Angular CLI
  - desc: SemVer
    url: https://semver.org/
toc:
  title: Angular Road Trip
  icon: icon-javascript
---

Today I want to blog about my Angular(2 - 6) experiences the past years.

After 3 years of desktop development I’ve started with Angular 2+ in july 2016, when it was still in beta. 

# Back then
{: .hide-from-excerpt}

Back then the landscape was a lot more difficult than it is today.
<!--more-->
- We did not have Angular-cli yet, we had to do the packing and configuration ourselves.
- We did not have a lot of documentation, and the one we had was often out of date and misleading.
- Editors(VSCode, Webstorm, VS..) did not cope well with Angular yet. The intellisense was often lacking, you had to write import statements yourself (can you believe it?), and so on..
- We got Release Candidates that contained major breaking changes. *"WTF? [Semver](https://semver.org) much?"*
- Npm equaled pain. Back then even more than now. Packages were often not versioned in a way that made sense. 

<br/>

“expression has changed after it was checked”
One of the interviewing questions I ask candidates is if they know this error message that would pop up in the console window in development mode. 
For me it’s an indicator of their experience with Angular. If you have never received this error you have never went through hell and back.

<br/>

In the beginning it was still suggested to use SystemJs instead of WebPack.
And then things became silent around SystemJs. The community was dying to hear from the Angular team what they’d choose next as the preferred packing mechanism.

<br/>

I remember asking the question to Igor Minar myself, one of the Angular project leads, on the first NG-BE conference ever. His eyes rolled to the back of his head. 
Apparently wherever he went people would ask that question and he got tired of it, especially because he could not give a clear answer yet. 
I thought I’d be smart and asked what google is using theirselves? Apparently they used their own mechanism. Making a possible answer even more complicated. 
It was not decided that day. But later it was made clear that Webpack would be embraced as the preferred packaging mechanism.

<br/>

The most important session at NB-BE for me was the session of Todd Moto.
He talked about forms in Angular. A session we welcomed because we had quite a struggle with that. So many possibilities and so little answers. 
Soon we realised that for business applications heavily depending on forms and validations Reactive Forms would be the way to go in most cases. 
But how do you implement it when you have a tree of components who all contain a part of the form and you have only one save button at the bottom of the page? 
I have a solid idea now, but only because it’s the logical result of a road full of trial & error.

<br/>

# As of today
I’m still glad I can work with Angular every day.

- The framework embraces TypeScript which is a revelation compared to JavaScript.
- I think it’s a great framework. Especially for full stack developers who can not fully dedicate their time to frontend development.
- It’s a rich and mature framework, still actively developed and each update comes with a lot of nice new features.
- The editors matured as well. Giving (almost) full support to the latest Angular versions.
- Flux implementations(ngrx, ngxs, ..) made it a lot cleaner to manage your data and have a single source of truth.
- You can aim for a minimal startup time and preload lazy loaded modules in the background with only little of configuration.
- Angular supports different configurations for different environments.
- Your code is minimized, uglified, and three-shaken.
- Angular-CLI gets you going within seconds. You can have a basic shell application built with the Angular framework and thus starting point for your newest project.
<br/>
All in all it's just a great framework for modern web applications and most of the time a joy to work with :)





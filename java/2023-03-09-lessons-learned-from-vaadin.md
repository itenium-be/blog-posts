---
layout: post
author: Onur Bugdayci
title: "Lessons learned from Vaadin"
subTitle: "A look at my journey with Vaadin"
date: 2023-03-09
categories: java
tags: [war-story]
img:
  url: vaadin.png
toc:
  icon: icon-java
  title: Lessons learned from vaadin
---


In 2022 I had the opportunity to work for a client who built the frontend of their web application in a
Java/TypeScript framework called Vaadin. In this article, I have compiled my thoughts on this curious framework.

<!--more-->

# My introduction to the ecosystem

My journey started with the preparation of course. I had heard and to a certain extent played with the usual frontend frameworks like React and Angular.
Outside of those, you would maybe hear of Vue but beyond that, you are kind of in the not-so-popular territory where frontend frameworks lie that isn't used a lot in a professional sense. So to encounter one in a professional setting was a surprise and it must have some kind of draw to get developers to use it over the popular ones.

# The draw

The draw of course is that it's a framework where if you want to, you can solely develop all your code in Java. As Vaadin itself puts it:

>Build modern web apps 100% in Java.
><br>Vaadin Flow is a unique full-stack framework that lets you build web apps without writing HTML or JavaScript.

For Java developers, I would say that's one big reason to opt for Vaadin. But adding to that, you need all the usual stuff for a frontend framework to thrive.
Extensive documentation, an active and thriving community of developers, tutorials and guides to get started on working with the framework.

# How I prepared

I'm a doer by nature so I learn mostly by doing stuff. With Vaadin it was more or less no different. I started with the Vaadin Foundation and Professional courses in their learning centre. A nice thing about these courses is that at the end of them, you can take an online exam provided by Vaadin and if you pass you are awarded a certificate you can show to potential clients. It was also a good indicator to my client then to see if I had the necessary skills to start on their project. Beyond that, I played around with their starter project to test what is and isn't possible.

# The client project

The client had developed this web application called Infopoint. The Infopoint application exists to handle the business needs of workers and customers in the physical stores of the client. So that ranges from simply looking up products to requesting stock from warehouses, creating and following up on orders, handling after-sales services and more.

The project at this client was to upgrade their existing Infopoint web application from Vaadin version 8 to 22. This involved changing, finding and creating alternatives to a lot of web components used in the views from the previous version. I also had to add new features here and there based on a backlog of business requirements that were now possible to be picked up in the upgrade.

On top of all that I also did support for the current version in production alongside the other developers on the team with me.

# Upgrading an old view

This was the main task in every sprint. I would pick up some assorted views, alongside any features pertaining to a view and I would go about upgrading it and/or implement the features. I would normally start with the template. The template is basically the structure of a view where all the components are defined. The old version created these templates in html and in the new version these needed to be converted to TypeScript. I used a handy tool from Vaadin for this part called Vaadin Designer. It was an easy way to drag & drop components onto a template file. Now mind you, I still had to do all of the stylings manually. Once the template was set, I add all the necessary IDs to the components. The components with an ID are then added to the Java Companion File. This file is created with each template file. The purpose of the Java Companion File is that it acts as a gateway for the Java side to communicate with the template TypeScript side. The Java Companion File has all the components with an ID from which I can create the view in Java.
Once the view is created I engineer and sometimes reverse engineer, in unfortunate cases, the business flows in the old version and implement the new features if there are any. Once that is done, I run some predefined tests and move the ticket on. Besides upgrading, I also had to do a lot of bug fixing and support but that's not as niche or interesting to explain as the upgrade workflow.

# Positives

Working in Vaadin as a Java developer is very familiar. Some of the stuff I discovered I could do in Vaadin I discovered by treating it like a normal Java Spring Boot application. Vaadin is built on top of Spring Boot so that added to its familiarity. If I wanted to know the API functionalities of a particular class I could easily click through definitions or check the Vaadin documentation which is itself also built like a Javadoc. It has a well-defined project structure built upon a normal Spring Boot application project structure. It's also easy to scale like you would any Spring Boot application. But besides those points and mainly from a business point of view, if you already have a lot of Java developers on your team, it's an easier and more cost-effective way to let your developers learn and do frontend work.

# Negatives

Because it is built on top of Spring Boot, it shares some of its negatives as well. The initial setup time takes a long while. Partly because the app has become quite large, but also because the Spring side and frontend side is built/compiled separately in the dev environment. Also, the frontend side is built using npm which would sometimes become corrupt if you built your application in the incorrect order which meant that you would need to remove the node_modules package, invalidate your caches and restart your ide and afterwards rebuild your application and wait for your ide to index everything. So you can see how that would become frustrating after a while. Other than that, live reloading doesn't work in some cases, mainly when you make small changes like styling to the frontend side. Another quite big technical limitation was the fact that you couldn't access the child elements of a component in the template file in the new version, this used to be possible in the old version. This had a major impact on my development as accessing child components from a template component was a functionality that was regularly used in the entire application, not least of which was the translation of a whole view and its child components from the topmost parent component. This had to be reworked everywhere so all translation was done per component.

# Conclusion

Like any framework, you can debate and discuss whether it's better or worse to use over others but ultimately it comes down to your personal preference and in a professional sense if it's a viable possibility like with my client. For me personally, as a Java developer, it was a great gateway and introduction to professional frontend development.
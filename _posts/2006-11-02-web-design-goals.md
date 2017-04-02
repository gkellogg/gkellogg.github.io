---
id: 40
title: Web Design Goals
date: 2006-11-02T20:00:31+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=40
permalink: /2006/11/web-design-goals/
categories:
  - Design
---
Working with legacy software systems provides its own considerations, but there are some general goals that can be stated for web-based software applications. To the degree that the nature of system upgrades substantially touches these areas of system design, it is important to consider the following goals within the system design. Well designed applications should meet the following goals in order to provide lasting value:

  * **Be robust** &#8211; Enterprise software is important to an organization. Users expect it to be reliable and bug-free. Therefore, it is incumbent on the design team to use the best possible software practices build robust solutions and ensure that the code is of the highest quality.
  * **Be performant and scalable** &#8211; Enterprise systems must meet the performance expectations of their users. They must also exhibit sufficient scalability â€“ the potential for an application to support increased load, given appropriate hardware.
  * **Take advantage of OO design principles** &#8211; Object Oriented (OO) design principles offer proven benefits for complex systems. Good OO design practice is promoted by the use of proven _design patterns_ – recurring solutions to common problems. The concept of design patterns was popularized in OO software development in [Design Patterns: Elements of Reusable Object-Oriented Software](http://www.awprofessional.com/bookstore/product.asp?isbn=0201633612&rl=1).
  * **Avoid unnecessary complexity** &#8211; Practitioners of **Extreme Programming (XP)** advocate doing _the simplest thing that could possibly work_. The lesson is to be wary of excessive complexity. Complexity adds to costs throughout the software lifecycle. On the other hand, thorough analysis must ensure that we don&#8217;t have a naive and simplistic view of requirements.
  * **Be maintainable and extensible** &#8211; Maintenance is by far the most expensive phase of the software lifecycle. It&#8217;s particularly important to consider maintainability when designing Enterprise systems. A well designed application will be useful to an organization for years and must be able to accommodate new business needs. Maintainability and extensibility depend largely on clean design. We need to ensure that each component of the application has a clear responsibility, and that maintenance is not hindered by tightly-coupled components.
  * **Be delivered on time** &#8211; The objectives of clean design must not eclipse the need for an efficient and productive development cycle. The use of development environments and frameworks that ease the development process and help implement proven design patterns is an important part in considering a development project.
  * **Be easy to test** &#8211; Testing is an essential activity throughout the software lifecycle. We should consider the implications of design decisions for ease of testing.
  * **Promote reuse** &#8211; Enterprise software must fit into an organization&#8217;s long term strategy. Thus it&#8217;s important to foster reuse, so that code duplication is minimized. Code reuse usually results from good OO design practice, while we should also consistently use valuable infrastructure provided by the application server where it simplifies application code.

(Portions of this entry are derived from [J2EE Design and Development](http://www.wrox.com/WileyCDA/WroxTitle/productCd-0764543857.html) by [Rod Johnson](http://blog.springframework.com/rod/).)
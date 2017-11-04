---
id: 18
title: Managing large projects
date: 2006-10-31T14:10:02+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=18
permalink: /2006/10/managing-large-projects/
categories:
  - Design
---
Of paramount concern in managing large development efforts is the need to ensure that the application remains relevant and maintainable and can be kept up-to-date with the requirements of the rest of the organization. The danger of a long running project managed by a limited pool of developers is that the original requirements and goals of the system can be lost in the face of multiple enhancements. This can lead to a system that is arcane, does not reflect the actual needs of the department, and (most importantly) is over-reliant on the internal knowledge of the development team. This poses a substantial risk to the business that relies on the system for day-to-day operation. Fortunately there is a long standing body of _Industry Best Practices_ that have been used to successfully manage the lifecycle of large-scoped projects. h3. Industry Best Practices The principal means of managing large software projects is to ensure that the problem domain and the software system are well understood. In a corporate sense, this means that the workflow, project history, system concept, design and implementation are fully documented and kept up-to-date throughout the product lifecycle. Such practice requires diligent management, as it is in the nature of people and organizations to lapse into ad-hoc knowledge and provincialism.

#### Communicate with Stakeholders

Identifying who the stakeholders of the system are and involving them intimately in the design process is key to ensuring the successful outcome of any development project. A software system can be coded to the highest standards, completely tested and run quickly and without bugs, but if it doesn&#8217;t actually solve a problem the end users need, it may as well never have been started. Stakeholders include end users, management, system architects, developers and system administrators. They also should include stakeholders in other systems that must interface with the system being designed.

#### Understand the Problem Domain

Create a concise statement of the business practices the system is addressing. This Concept Document should lay out the high-level requirements of the system in a way that the stakeholders can use to understand their relationship to it.

#### Involve Stakeholders

Create Use Cases to describe the interaction of stakeholders with the system. In conjunction with the Concept Document, this will result in a detailed Requirements Document. This process should be interactive with the different stakeholders so that the fully understand the purpose of the system and how they will relate to it. This is a principle opportunity to ensure that everyone is "on the same page".

#### Further Documentation

Functional Specifications and Technical Specifications serve to embody the application in a prose-like instantiation. In particular, the functional specification must accurately reflect the modules and interfaces to the system. This is particularly useful for Quality Assurance to provide an independent measure of the success of the project. Technical Specifications are often embodied directly within the code. Various tools can be used to extract documentation directly from the living code to ensure that it is easy to keep up to date.

#### Coding Standards

Whatever the development environment, it is important to adopt and adhere to specific coding standards. This includes specifics of formatting and commenting code, but can also include specific design patterns.

#### Quality Assurance

It is useful to have a separate quality assurance program to provide an independent analysis of the project to ensure that it has lived up to its stated design goals and functions according to specification. h4. Iterative Development Many problems are too big to be developed all at once. When working with stakeholders, It's useful to prioritize requirements and do a "phased" implementation starting with a subset of the product features. Further requirements can be handled in an iterative approach over the course of time. The Scrum development methodology embodies most of these elements. The principal is to organize development using fixed-increment (typically 30-day) phases. Requirements are prioritized and analyzed for inclusion at the beginning of the phase (called a Sprint). Short daily meetings are held to communicate progress from the previous day and goals for the next day and to identify issues that are in the way of achieving these goals. This results in a system that starts up quickly and achieves added functionality over time. The Scrum process can be used for both documentation and development efforts. For a good description of Scrum check see [www.controlchaos.com](http://www.controlchaos.com/).

Gregg Kellogg
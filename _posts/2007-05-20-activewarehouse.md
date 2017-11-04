---
id: 48
title: ActiveWarehouse
date: 2007-05-20T01:10:51+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=48
permalink: /2007/05/activewarehouse/
categories:
  - Ruby
---
Related to the interesting talk at a recent NBRug meeting on Ruport, I've been looking at doing data warehousing in Ruby. Fortunately, [Anthony Eden](http://anthonyeden.com/) has beaten me to the punch with [ActiveWarehouse](http://activewarehouse.rubyforge.org/). For those unfamiliar with Data Warehousing, the concepts are basically to create a series of _facts_ that are indexed buy multiple _dimensions_.

  * A _fact_ is typically an integer (e.g., sales amount) with data relating to the fact expressed in _dimensions_. (Note that in some cases, you can have a _Factless Fact_ table where the information is entirely in the dimensions).
  * _Dimensions_ provide different types of data relating to the fact (e.g., date & time of entry, user, product, location, etc.), so a _fact_ table has a column for each _dimension_ with a column for the fact scalar value itself.

The `dimension` tables contain an `id` index column, a column for the fact value (e.g., timestamp for date & time) and columns for each _summary_ to be associated with the _dimension_ (e.g., day of week, day of month, month, year, hour minute, &#8230;). At this point, queries can be performed by joining the dimension and fact tables and summarizing (or counting) the scalar fact value against conditions against the dimension tables (e.g., sales per year by person and region).

Typically, this data is pre-summarized in _cubes_ with summary tables that contain the results of these summary queries. The **ActiveWarehouse** plugin contains everything necessary to define, populate and report on this data.

I expect to be using this to hold information on user listening habits for MP3 files.
---
id: 137
title: Microdata parser for RDF.rb
date: 2011-06-30T03:00:28+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=137
permalink: /2011/06/microdata-parser-for-rdf-rb/
categories:
  - Ruby
  - Semantic Web
---
I&#8217;ve just release version 0.1.0 of the RDF::Microdata gem ([rubygems](http://rubygems.org/gems/rdf-microdatea), [github](https://github.com/gkellogg/rdf-microdata)) for the [RDF.rb](http://github.com/bendiken/rdf) suite. This version contains only a reader (parser). Writing is not supported at this point.

Use it pretty much like any other RDF.rb reader:

    graph = RDF::Graph.load("etc/foaf.html", :format => :microdata)
    

Feedback either to me, or [public-rdf-ruby](mail:public-rdf-ruby@w3c.org).
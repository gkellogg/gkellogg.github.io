---
id: 144
title: RDF::N3 no longer accepts text/turtle or :ttl
date: 2011-09-05T23:36:51+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=144
permalink: /2011/09/rdfn3-no-longer-accepts-textturtle-or-ttl/
categories:
  - Ruby
  - Semantic Web
---
With the release of [RDF::Turtle](http://rubygems.org/gems/rdf-turtle), starting with version 0.3.5, [RDF::N3](http://rubygems.org/gems/rdf-turtle) no longer asserts that it is a reader for Turtle. This includes MIME Types text/turtle, application/turtle, application/x-turtle. Or the .ttl extension or :ttl or :turtle formats. Of course, N3 remains reasonably compatible with Turtle, but the recent RDF 1.1 Working Group publication of the [Turtle Specification](http://www.w3.org/TR/2011/WD-turtle-20110809/) has caused some divergence.

Most notably, in Turtle, the empty prefix (&#8216;:') is no longer a synonym for <#>. In fact, the empty prefix is no longer defined by default.

[RDF::Turtle](http://rubygems.org/gems/rdf-turtle) defines MIME Types text/turtle, text/rdf+turtle, application/turtle and application/x-turtle.

The officially submitted MIME Type for Turtle is text/turtle with default content coding of UTF-8.

As usual, you can try both these and other RDF gems a the [distiller](http://rdf.greggkellogg.net/distiller/) At some point, [RDF::Turtle](http://rubygems.org/gems/rdf-turtle) will make it into the [linkeddata gem].
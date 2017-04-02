---
id: 174
title: RDF.rb and SPARQL gem updates
date: 2013-05-11T23:12:21+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=174
permalink: /2013/05/rdf-rb-and-sparql-gem-updates/
categories:
  - Ruby
  - Semantic Web
---
<span itemprop="name" style="display:none">RDF.rb and SPARQL updates</span>
  
<span itemprop="description">Version 1.0.6 of RDF.rb and 1.0.7 of SPARQL gems released.</span>

I recently pushed updates to [RDF.rb](http://rubygems.org/gems/rdf) and [SPARQL](http://rubygems.org/gems/sparql) gems. These updates contain some useful new features:

## RDF.rb

The main RDF gem has many updates to bring it closer to the coming update to the RDF 1.1 specifications ([RDF Concepts](http://www.w3.org/TR/rdf11-concepts/), [RDF Semantics](http://www.w3.org/TR/rdf11-mt/), [Turtle](http://www.w3.org/TR/turtle/), [TriG](http://www.w3.org/TR/trig/) [N-Triples](http://www.w3.org/TR/n-triples/), [N-Quads](http://www.w3.org/TR/n-quads/), and [JSON-LD](http://json-ld.org/spec/latest/)). Notable changes since the 1.0 release are:

  * Make distinction between Plain and Simple literals; Simple literals have no language, plain literals may also have a language. And, in preparation for RDF 1.1, literals having a datatype of xsd:string are considered Simple literals. Language-tagged literals may have a datatype of rdf:langString.
  * Improved support for queries using hash patterns (thanks to @markborkum).
  * Update [N-Triples](http://www.w3.org/TR/n-triples/) (and [N-Quads](http://www.w3.org/TR/n-quads/)) readers and writers to support the RDF 1.1 version, including new escape sequences and support for UTF-8, in addition to ASCII. When writing characters are escaped depending on the specified encoding or that inferred from the output file.
  * Term and Statement comparison is dramatically improved improving statement insertion and query performance.
  * Other literal changes required to support SPARQL 1.1

On the 1.1 branch:

  * RDF::URI re-written to not require Addressable. This improves general performance of using URIs by about 50% (depends on 1.9+ features, so not included in the 1.0 branch).
  * Support for Ruby versions less than 1.9.2 is dropped.

## SPARQL

The [SPARQL](http://rubygems.org/gems/sparql) gem is not based on the SPARQL 1.1 grammar, and now includes some features from SPARQL 1.1, including all functions and builtins and variable bindings. Look for new features to be added incrementally; once a critical mass is reached, I&#8217;ll update the gem version to 1.1 to reflect that this is essentially a 1.1 compatible version of SPARQL. Eventually this will include SPARQL Update and Federated Queries as well.

## Other gems

  * [JSON::LD](http://rubygems.org/gems/json-ld) is released as version 1.0.0 and is full compatible with the last-call version of the [JSON-LD](http://json-ld.org/spec/latest/) specifications, including support for framing.
  * [RDF::Turtle](http://rubygems.org/gems/rdf-turtle) is fully compatible with the RDF 1.1 version. 
      * Also includes a [Freebase](https://developers.google.com/freebase/data)-specific reader for fastest performance reading Freebase dumps.
  * [RDF::RDFa](http://rubygems.org/gems/rdf-rdfa) is compatible with [RDFa Core 1.1](http://www.w3.org/TR/rdfa-core/) and [HTML+RDFa 1.1](http://www.w3.org/TR/rdfa-in-html/)
  * [RDF::TriG](http://rubygems.org/gems/rdf-trig) is released as a 1.0.0 version, based on the RDF 1.1 note
  * [RDF::Raptor](http://rubygems.org/gems/rdf-raptor) now uses Raptor 2.0, and is fully compatible with the latest version of [Redland/Raptor](http://librdf.org/raptor/).
---
id: 150
title: SPARQL 1.0 for Ruby
date: 2011-10-03T16:34:42+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=150
permalink: /2011/10/sparql-1-0-for-ruby/
categories:
  - Ruby
  - Semantic Web
---
I&#8217;ve just released version 0.0.2 of the Ruby [sparql gem](http://rubygems.org/gems/sparql). This version is based on earlier work by Pius and Arto and incorporates from [SPARQL Grammar](http://github.com/gkellogg/sparql-grammar) and [SPARQL Algebra](http://github.com/gkellogg/sparql-algebra). Further documentation is available [here](http://rdoc.info/github/gkellogg/sparql/master/frames).

This gem integrates with \[RDF.rb\]\[\] and uses \[rdf-xsd\]\[\] to provide additional literal semantics.

Why release SPARQL for Ruby? Probably not because of the killer performance, at least right now. However, I believe it&#8217;s important that Ruby have a complete tool chain for manipulating Linked Data (including RDF and SPARQL), and this was the remaining piece.

In spite of the 0.0.2 release number, is is a fully functioning implementation of SPARQL 1.0 semantics and passes all the DAWG data-r2 test cases. The gem makes use of RDF::Query to perform basic BGP operations on RDF::Queryable objects (such as RDF::Repository). The gem has some support for query optimization, but this remains largely unimplemented and will be addressed in future releases. I&#8217;d also like to support SPARQL 1.1 queries and udpates at some point.

This is a pure Ruby implementation and does not directly rely on any native libraries (although, some RDF readers such as RDFa and RDF/XML presently do).

The basic strategy is to parse SPARQL and transform it into an S-Expression-based algebra, pretty close to that used by [Jena ARQ](http://jena.sourceforge.net/ARQ/algebra.html) (SPARQL S-Expressions, or SSE). This allows SSE to be used directly for performing queries, or to parse SPARQL grammar to SSE.

The [linkeddata](http://rubygems.org/gems/linkeddata) gem has also been updated to have a soft reference to SPARQL, in addition to new processors for RDF::Turtle, JSON::LD, and RDF::Microdata.

The gem is tested on Ruby 1.8.7, 1.9.2 and JRuby. (JRuby has some spec issues, probably due to Nokogiri differences)

Many thanks to Pius Uzamere and helping to make this release happen, and to Arto Bendiken for the work in RDF.rb, SPARQL::Algebra and SPARQL::Grammar that preceded this.
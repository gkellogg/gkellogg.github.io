---
id: 113
title: SPARQL Algebra
date: 2011-03-04T12:17:32+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=113
permalink: /2011/03/sparql-algebra/
categories:
  - Ruby
  - Semantic Web
---
For those intrepid enough, I&#8217;ve pushed version 0.0.2 of [sparql-algebra](https://github.com/gkellogg/sparql-algebra). It relies on unreleased changes to RDF.rb and sxp-ruby, so you need to use bundler with the included Gemfile.

SPARQL Algebra implements the s-expression-based SPARQL algebra described in [SPARQL 1.1](http://www.w3.org/TR/rdf-sparql-query/#sparqlAlgebra) and [Jena](http://openjena.org/wiki/SSE). Remaining work needed for &#95;describe&#95; operator and query optimizations. This is the base for translation from SPARQL Grammar [4], which requires just a bit more work to be fully compliant. Both of these, along with support for an HTTP endpoint and solution serializer, will be sufficient to implement a complete SPARQL solution in pure Ruby.

SPARQL Algebra passes all but four W3C DAWG tests (data-r2), with those four not being worth implementing, in my opinion. As an example of an SSE based on the SPARQL grammar, consider the following:

    PREFIX  foaf:  <http://xmlns.com/foaf/0.1/>
    
    SELECT ?mbox ?name
     {
       ?x foaf:mbox ?mbox .
       OPTIONAL { ?x foaf:name  ?name } .
     }
    

which is equivalent to the following SSE:

    (prefix ((foaf: <http://xmlns.com/foaf/0.1/>))
       (project (?mbox ?name)
         (leftjoin
           (bgp (triple ?x foaf:mbox ?mbox))
           (bgp (triple ?x foaf:name ?name)))))
    

There are outstanding pull requests to RDF.rb and sxp-ruby that are required to release it to RubyGems, but you&#8217;re encouraged to play with it and send feedback!

Thanks to Arto and Ben for the initial work they did on this, and other enabling projects, as well as creating an excellent executable test suite!

### Update

`SPARQL::Grammar` now complete, generating `SPARQL::Algebra` classes, allowing a complete end-to-end SPARQL solution for Ruby.
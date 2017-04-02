---
id: 90
title: RdfContext version 0.5.0 brings compliant notation-3/turtle parsing
date: 2010-02-23T01:33:49+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=90
permalink: /2010/02/rdfcontext-version-0-5-0-brings-compliant-notation-3turtle-parsing/
categories:
  - Ruby
  - Semantic Web
---
This version of 0.5.0 adds support for Ruby 1.9 and 1.8.6 in addition to 1.8.7. Full unicode character escapes are supported in Ruby 1.9. The N3 parser has been substantially re-written and updated to be compliant with W3C SWAP, CWM and Turtle test suites, although it is supported only to the N3-rdf level, with variables and formulae not yet supported.

See [GitHub project page](http://github.com/gkellogg/rdf_context) for more information. Available for download through Gemcutter.

Release notes for version 0.5.0

  * Support for Ruby 1.9, 1.8.7 and 1.8.6. 
      * Unicode escapes and URIRefs only work properly in Ruby 1.9
      * Replaced some (&:meth) spells with {|c| c.meth} for Ruby 1.8.6 compatibility
  * Fully compliant N3 parser (n3-rdf level) with extensive tests, including SWAP, CWM and Turtle test suites. 
      * Supports paths, keywords, all semantic expressions.
      * No support yet for Formulae, variables, rules or automatic reification
      * Allow `Triple#subject` to be like an object, allowing literals and graphs
      * Allow `Triple#predicate` to be a _BNode_ as well as a _URIRef_
  * Graph changes 
      * `Graph#properties(subject)` returns properties of a subject expressed as a hash to arrays of objects.
      * `Graph#seq(subject)` returns ordered `rdf:_n` objects if subject is a `rdf:Seq`, or list of `rdf:first/rdf:rest`
      * `Graph#qname(uri)` as alternative to `uri.qname`, has namespaces available.
      * `Graph#type_of(subject)` array of `RDF_TYPE` objects.
      * `Graph#allow_n3` (getter/setter and option) controls if extra N3 semantics for graphs are allowed. Otherwise, calls `Triple#validate_rdf` to raise exception
      * Real graph comparisons, including permutation search of triples containing _BNodes_ (obviously, may be expensive)
      * Add `QuotedGraph` and `AggregateGraph`
  * Literal changes 
      * `Literal#==` as alias to `eql?` Needed for `sort` and `uniq`.
      * Normalize valid typed literals
      * Added `Literal#valid?` to perform some content validations.
  * _URIRef_/_Namespace_ changes 
      * Fix URI generation, performing normalizations for normal URI refs, and not for Namespace URIs.
      * Fixed bug in `URIRef#namespace` and `to_qname` when namespace does not have a trailing / or #
      * `URIRef#short_name` may return a different value after a namespace is assigned.
  * Reduce dependency on Redland when running tests
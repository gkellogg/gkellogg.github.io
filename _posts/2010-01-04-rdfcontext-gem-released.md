---
id: 83
title: RdfContext gem released
date: 2010-01-04T01:40:03+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=83
permalink: /2010/01/rdfcontext-gem-released/
categories:
  - Ruby
  - Semantic Web
---
I&#8217;ve released version 0.4.4 of the [RdfContext](http://gemcutter.org/gems/rdf_context) gem. As the name implies, RdfContext supports contextual data-stores bound to graphs, along with a `ConjunctiveGraph` providing the union of contexts within a given data-store.

  * Parses RDF/XML, RDFa and N3. RDF/XML and RDFa both pass all relevant W3C test cases (may be run through specs).
  * `Graph` and `ConjunctiveGraph` with pluggable data-stores. `MemoryStore` and `SQLite3Store` both support _contexts_ as well as _quoted-graphs_ and _formulae_, although no appropriate graph classes yet exist.
  * Graphs serialize to _N-Triples_ and _RDF/XML_.
  * An RDF distiller runs on this site to test out different parsers. This is also useful for running automated RDFa Test Harness. 

RdfContext is based, in part on Tom Morris&#8217; [Reddy](http://gemcutter.org/gems/reddy) gem. See the [readme](http://github.com/gkellogg/rdf_context) on GitHub for more information. `MemoryStore`, `SQLite3Store` and `ConjunctiveGraph` are largely ports of Python RDFLib.
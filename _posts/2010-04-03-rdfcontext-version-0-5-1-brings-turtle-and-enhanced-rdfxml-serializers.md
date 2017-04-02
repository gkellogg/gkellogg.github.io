---
id: 93
title: RdfContext version 0.5.1 brings Turtle and enhanced RDF/XML serializers
date: 2010-04-03T07:57:32+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=93
permalink: /2010/04/rdfcontext-version-0-5-1-brings-turtle-and-enhanced-rdfxml-serializers/
categories:
  - Ruby
  - Semantic Web
---
Just pushed version 0.5.1 of [RdfContext](http://github.com/gkellogg/rdf_context) to GitHub and [Gemcutter](http://gemcutter.org/gems/rdf_context). This version includes a Serializer framework, including a `AbstractSerializer`, `RecursiveSerializer` and Turtle and RDF/XML serializers based on these. The RDF/XML serializer is a big improvement over the previous version, including Typed element names an RDF Container folding using `parseType="collection"`.

RdfContext includes native Ruby parsers for RDF/XML, RDFa and N3-rdf, which includes Turtle and N-Triples. All parsers pass W3C tests (included in specs). It also includes context-aware quad store, with in-memory and SQLite3 storage models.
---
id: 245
title: Release 1.1.0 of Ruby RDF libraries
date: 2013-12-06T20:59:03+00:00
author: gregg
layout: post
guid: https://greggkellogg.net/?p=245
permalink: /2013/12/release-1-1-0-of-ruby-rdf-libraries/
categories:
  - Ruby
  - Semantic Web
---
I'm happy to announce release 1.1.0 of the Ruby RDF suite, this includes support for RDF 1.1 Concepts and syntaxes, including RDF Literal changes, N-Triples, N-Quads, TriG and JSON-LD. Additionally, the SPARQL gem has been updated to support RDF 1.1 differences (mainly differences in plain literals vs those with xsd:string datatypes and the fact that language-tagged literals now have the rdf:langString datatype).

There are some incompatibilities with previous releases described below:

  * Support for Ruby 1.8.* has been eliminated from all gems, however the latest versions of JRuby and Rubninus are supported.

  * RDF.rb: <https://github.com/ruby-rdf/rdf>
    
      * Works on versions of Ruby >= 1.9.2 (1.8.* support now dropped), this includes JRuby and Rubinius 2.1+ (true for all gems)
      * Support for all RDF 1.1 concepts (other than skolumization, if anyone's really interested in this, we could add support in a future release),
      * Native implementation of RDF::URI (aka RDF::IRI) without using Addressable::URI; this is actually a big performance win, as URI's typically don't need to be parsed now,
      * RDF::Graph cannot take a context (name) unless it is a projection of a named graph from an RDF:: Repository (aka RDF::Dataset),
      * Support for 1.1 versions of N-Triples and N-Quads
      * Support for changes to Literal semantics, meaning that all literals have a datatype. #plain? and #simple? accessors still exist, which have expected results.
      * RDF::Query#execute now accepts a block and returns RDF::Query::Solutions. This allows enumerable.query(query) to be have like query.execute(enumerable) and either return an enumerable of yield each solution.
      * RDF::Queryable#query returns a concrete Enumerator extending RDF::Queryable and RDF::Enumerable or RD::Query::Solutions; this improves performance when accessing enumerator methods as they're not extended dynamically (see issue #123: "Performance and Kernel#extend")
      * RDF::Util::File.open_file (used for Mutable.load, among others) no honors 303 and Location: headers to maintain the source identifier and try to manage all input data as UTF.
      * RDF::StrictVocabulary is now added as a sub-class of RDF::Vocabulary; most built-in vocabularies are now strict, meaning that an attempt to access a term not defined in the vocabulary will raise an error. Many more vocabuarlies added including iCal, Media Annotations, Facebook OpenGraph, PROV, SKOS-XL, Data Vocabulary, VCard, VOID, Power-S, XHV and schema.org.
      * New vocabulary definitions have been added for ICal, Media Annotations (MA), Facebook OpenGraph (OG), PROV, SKOS-XL (SKOSXL), Data Vocabulary (V), VCard, VOID, Powder-S (WDRS), and XHV.

  * RDF::N3: <https://github.com/ruby-rdf/rdf-n3>
    
      * No longer detects formats when a specific content type is not known; this prevents problems interpreting Turtle as N3.

  * RDF::RDFXML: <https://github.com/ruby-rdf/rdf-rdfxml>
    
      * Now fully supports JRuby; previously, the writer only worked on MRI and Rubinius. Writer now uses Haml templates, and extends the RDFa writer.

  * RDF::Turtle: <https://github.com/ruby-rdf/rdf-turtle>, RDF::TriG: <https://github.com/ruby-rdf/rdf-trig>
    
      * Full support for RDF 1.1 specifications

  * SPARQL: <https://github.com/ruby-rdf/sparql>
    
      * All query operators now accept a block for returning solutions in addition to returning the RDF::Query::Solutions.
      * Specific Queryable implementations can now optimize any part of the SPARQL Algebra execution chain by implementing the appropriate operator as all calls go through the RDF::Queryable object. This can be used by specific storage adaptors to implement part or all of the query chain using implementation-specific optimizations.
      * Minor test harness changes to tollerate RDF 1.1 changes.

  * Rack::LinkedData, Sinatra::LinkedData, Rack::SPARQL, Rack::Sinatra
    
      * All updated for latest versions of Rack and Sinatra. Note that Sinatra does not support returning the enumerable without a content type for content-negotiated serialization of the results, as Sinatra defaults results to text/html. Use linkeddata\_options (or sparql\_options) to set desired content type or format of result. Prioritized ACCEPT types provided through environment.

All gems now support only Ruby >= 1.9.2, JRuby and Rubinius.

This includes the following individual gems:

  * Core RDF and repositories:
    
      * rdf
      * rdf-aggregate-repo
      * rdf-do
      * rdf-isomorphic
      * rdf-mongo
      * rdf-spec

  * RDF Serialization formats
    
      * json-ld
      * rdf-json
      * rdf-microdata
      * rdf-n3
      * rdf-raptor
      * rdf-rdfa
      * rdf-rdfxml
      * rdf-trig
      * rdf-trix
      * rdf-turtle
      * rdf-xsd

  * Querying
    
      * sparql
      * sparql-client
      * spira

  * Rollup releases, including most of the above gems
    
      * linkeddata
      * rack-linkeddata
      * sinatra-linkeddata

All versions of this are running on my distiller: <http://rdf.greggkellogg.net/>. Any comments or issues found prior to release are appreciated.

I'd like to thank David Butler, Judson Lester, Justin Coyne, Slava Kravchenko, and Aymeric Brisse in particular for their active support in maintaining the Ruby RDF eco-system.
---
id: 110
title: RDF::RDFa, RDF::RDFXML, and RDF::N3 0.3.0 releases
date: 2010-12-28T00:25:51+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=110
permalink: /2010/12/rdfrdfa-rdfrdfxml-and-rdfn3-0-3-0-releases/
categories:
  - Ruby
  - Semantic Web
---
The Nokogiri-based reader suite for the RDF.rb environment. This version offers substantial performance gains, due to general improvements in RDF.rb as well as a number of improvements in the readers:

**General improvements**

  * Readers save prefix definitions in `:prefixes`. Writers use `:prefixes`, or `:standard_prefixes` to generate _QNames_.
  * Readers supports `:canonicalize` and `:validat`e options

**[RDF::N3](https://rubygems.org/gems/rdf-n3)**

  * New parser based on Tim-BL's Predictive Parser supports quoted graphs and variables.
  * Stream-based reader can process an indefinite length input file, vs. the older Treetop-based reader that was a two-pass parser.
  * Substantial performance improvement over previous version, running at about x statements/second on an iMac.
  * From History:
  *   * New Predictive-Parser based N3 Reader, substantially faster than previous Treetop-based parser
      * RDF.rb 0.3.0 compatibility updates 
          * Remove literal\_normalization and qname\_hacks, add back uri_hacks (until 0.3.0)
          * Use `nil` for default namespace
          * In Writer 
              * Use only `:prefixes` for creating _QNames_.
              * Add `:standard_prefixes` and `:default_namespace` options.
              * Use """ for multi-line quotes, or anything including escaped characters
          * In Reader 
              * URI canonicalization and validation.
              * Added `:canonicalize`, and `:intern` options.
              * Added `#prefixes` method returning a hash of prefix definitions.
              * Change `:strict` option to `:validate`.
              * Add check to ensure that predicates are not literals, It's not legal in any RDF variant.
      * RSpec 2 compatibility

**[RDF::RDFXML](https://rubygems.org/gems/rdf-rdfxml)**

  *   * RDF.rb 0.3.0 compatibility updates 
          * Remove literal\_normalization and qname\_hacks, add back uri_hacks (until 0.3.0)
          * Use `nil` for default namespace
      * In Writer 
          * Use only `:prefixes` for creating _QNames_.
          * Add `:standard_prefixes` and `:default_namespace` options.
          * Improve `Writer#to_qname`.
          * Don’t try to translate `rdf:_1` to `rdf:li` due to complex corner cases.
          * Fix problems with `XMLLiteral`, `rdf:type` and `rdf:nodeID` serialization.
      * In Reader 
          * URI canonicalization and validation.
          * Added `:canonicalize`, and `:intern` options.
          * Change `:strict` option to `:validate`.
          * Don’t create unnecessary namespaces.
          * Don’t use regexp to substitute base URI in URI serialization.
          * Collect prefixes when extracting mappings.
      * `Literal::XML` 
          * Add all in-scope namespaces, not just those that seem to be used.
      * RSpec 2 compatibility

**[RDF::RDFa](https://rubygems.org/gems/rdf-rdfa)**

  *   * RDF.rb 0.3.0 compatibility updates 
          * Remove literal\_normalization and qname\_hacks, add back uri_hacks (until 0.3.0)
          * Use nil for default namespace
      * In Writer 
          * Use only `:prefixes` for creating _QNames_.
          * Add `:standard_prefixes` and `:default_namespace` options.
          * Improve `Writer#to_qname`.
      * In Reader 
          * URI canonicalization and validation.
          * Added `:canonicalize`, and `:intern` options.
          * Change `:strict` option to `:validate`.
          * Don’t create unnecessary namespaces.
          * Don’t use regexp to substitute base URI in URI serialization.
          * Collect prefixes when extracting mappings.
      * `Literal::XML` 
          * Add all in-scope namespaces, not just those that seem to be used.
      * RSpec 2 compatibility
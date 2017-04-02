---
id: 107
title: RdfContext and RDF::RDFa support for RDFa 1.1 2008-08-03 draft semantics
date: 2010-09-02T01:53:49+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=107
permalink: /2010/09/rdfcontext-and-rdfrdfa-support-for-rdfa-1-1-2008-08-03-draft-semantics/
categories:
  - Ruby
  - Semantic Web
---
 I&#8217;ve updated both [RdfContext](http://github.com/gkellogg/rdf_context) and [RDF::RDFa](http://github.com/gkellogg/rdf-rdfa) gems to support the latest [RDFa Core 1.1 Editor&#8217;s Draft  (2010-08-03)](http://www.w3.org/TR/2010/WD-rdfa-core-20100803/) semantics. This includes support for the following:

  * Use of a _Processor Graph_ to gather information and errors during the course of parsing. Use the `:processor_graph` option to specify a Graph in which to collect information. There is no published specification for the properties to use, but until there is, each event is saved with a Blank Node subject of type **rdfa.UndefinedPrefixError**, **rdfa:UndefinedTermError**, **rdfa:HostLanguageMarkupError**, **rdfa:ProfileReferenceError**, **rdfa:InformationalMessage**, **rdfa**;MiscellaneousWarning or **rdfa:MiscellaneousError**. Additionally statements with literals for **dc:description**, dc:date, **rdfa:sequence** and **rdfa:source** (path to HTML Node) are generated.
  * _RDFa Profiles_ allow URI mappings for terms and prefixes along with the specification of a default vocabulary.
  * RDFa 1.1 prefixes the use of _@prefix_ to create prefix mappings, but _@xmlns_ continues to be supported.
  * XMLLiterals must be declared explicitly by setting _@datatype_=&#8221;rdf:XMLLiteral&#8221;. In 1.0, any statement with a property that contained anything other than text nodes as children caused an XMLLiteral to be emitted.

Note that until RDFa Core 1.1 is published, all features are subject to change. I will not be attempting to maintain compatibility with draft features that are obsoleted during the standardization process.
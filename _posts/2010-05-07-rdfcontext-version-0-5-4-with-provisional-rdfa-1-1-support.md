---
id: 100
title: RdfContext version 0.5.4 with provisional RDFa 1.1 support
date: 2010-05-07T07:25:47+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=100
permalink: /2010/05/rdfcontext-version-0-5-4-with-provisional-rdfa-1-1-support/
categories:
  - Ruby
  - Semantic Web
---
I just released version 0.5.4 of RdfContext to [GitHub](http://github.com/gkellogg/rdf_context) and [GemCutter](http://gemcutter.org/gems/rdf_context). This version is notable for including support for  [RDFa 1.1](http://www.w3.org/TR/2010/WD-rdfa-core-20100422/) parsing. This is still based on an Working Draft, so it will likely change in the future.

RDFa 1.1 includes support for profiles, vocabularies and terms. And supports using URIs or CURIEs or terms anywhere that's legal within an HTML document. Right now, only the [XHTML+RDFa](http://www.w3.org/TR/2010/WD-xhtml-rdfa-20100422/) profile is supported.

### Default term URI using @vocab.

RDFa 1.1 allows URIs to be expressed using an NCName, called a term, by using the `@vocab` attribute, an author can define a URI to be used for a bare word to turn it into a URI. Take for example the following:

    <div vocab="http://xmlns.com/foaf/0.1/">
       <p about="#me" typeof="Person" property="name">Gregg Kellogg</p>
    </div>
    

will generate the following triples:

    <#me> a foaf:Person;
      foaf:name "Gregg Kellogg" .
    

### Profile documents for defining prefixes and terms

A _Profile_ document allows the specification of a set of URI mappings and term mappings in a single document. These documents are RDF formatted, and may or may not be RDFa. The following shows an example _Profile_ document:

    @prefix rdfa: <http://www.w3.org/ns/rdfa#> .
    [ rdfa:prefix "foaf"; rdfa:uri "http://xmlns.com/foaf/0.1/"] .
    [ rdfa:prefix "dc"; rdfa:uri "http://purl.org/dc/terms/"] .
    [ rda:term "name"; rdfa:uri "http://xmlns.com/foaf/0.1/name"] .
    [ rda:term "created"; rdfa:uri "http://purl.org/dc/terms/created"] .
    

This bit profile results in namespace mappings and a bare terms. Multiple vocabularies may be used together to create a namespace composed of terms from several vocabularies, without needing to describe them explicitly. These may then be used in a document as follows:

    <div profile="http://example.com/my_vocab">
      <p about="#me">
        <span property="name">Gregg Kellogg</span>
        is the author of
        <a rel="created"
            resource="http://github.com/gkellogg/rdf_context">
          RdfContext
        </a>
      </p>
    </div>
    

### Namespace definitions

RDFa 1.1 deprecates the use of @xmlns for defining namespace prefixes. The `@prefix` attribute defines one or more mappings between prefixes and URIs. For example:

    <div prefix="foaf: http://xmlns.com/foaf/0.1/ dc: http://purl.org/dc/terms/">
      <p about="#me">
        <span property="foaf:name">Gregg Kellogg</span>
        is the author of
        <a rel="dc:created"
            resource="http://github.com/gkellogg/rdf_context">
          RdfContext
        </a>
      </p>
    </div>
    

Defines and uses two different namespace mappings.

### URIs Everywhere

In RDFa 1.0, certain attributes took a URI, others a CURIE, and still others either a URI or a Safe CURIE. This is confusing, and RDFa 1.1 now allows either URIs, CURIEs, or SafeCURIEs to be used most anywhere (SafeCURIEs are maintained for backwards compatibility). For example:

    <div>
      <p about="#me">
        <span property="http://xmlns.com/foaf/0.1/name">
          Gregg Kellogg
        </span>
        is the author of
        <a rel="http://purl.org/dc/terms/created"
          resource="http://github.com/gkellogg/rdf_context">
          RdfContext
        </a>
      </p>
    </div>
    

### Change History

The following is the change log for this version of RdfContext. Note that one change may potentially break existing code: `URIRef#namespace` no longer throughs an exception if a mapping is not found. Other changes are noted here:

  * RDFa 1.1 parsing supported (based on RDFa Core 1.1 W3C Working Draft 22 April 2010) 
  * Fix URIRef#short\_name (and consequently #base and #namespace) to not extract a non-hierarchical path as a short\_name 
  * Namespace no longer uses URIRef, but just acts on strings. 
  * Namespace#new does not take an optional &#95;fragment&#95; argument any longer. 
  * Added Namespace#to_s to output "prefix: uri" format 
  * Graph#qname first trys generating using bound namespaces, then adds well-known namespaces. 
  * URIRef#to\_qname and #to\_namespace No longer generates an exception. Each take either a Hash or an Array of namespaces and tries them from longest to shortest. 
  * Improved Turtle and XML serializers in use of namespaces. 
  * Generate pending messages if RDFa tests skipped due to lack of Redland installation. 
  * Change dcterms: prefix to dc: (fully compatible with previous /elements/ definitions)
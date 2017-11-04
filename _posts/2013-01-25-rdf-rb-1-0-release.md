---
id: 172
title: RDF.rb 1.0 Release
date: 2013-01-25T20:40:22+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=172
permalink: /2013/01/rdf-rb-1-0-release/
categories:
  - Ruby
  - Semantic Web
---
I'm happy to announce the 1.0 release of [RDF.rb](http://ruby-rdf.github.com/rdf) and related Ruby gems. This release has been a long time coming, and the library has actually been quite stable for some time.

[RDF.rb](http://ruby-rdf.github.com/rdf) is a [Ruby Gem](http://rubygems.org/) implementing core \[RDF\]\[\] concepts, such as [Graph](http://rubydoc.info/github/ruby-rdf/rdf/RDF/Graph), [Repository](http://rubydoc.info/github/ruby-rdf/rdf/RDF/Repository), [Statement](http://rubydoc.info/github/ruby-rdf/rdf/RDF/Statement), and [Query](http://rubydoc.info/github/ruby-rdf/rdf/RDF/Query). It also supports core [readers](http://rubydoc.info/github/ruby-rdf/rdf/RDF/Reader) and [writers](http://rubydoc.info/github/ruby-rdf/rdf/RDF/Writer) (parsers and serializers) for [N-Triples](http://en.wikipedia.org/wiki/N-Triples) and [N-Quads](http://sw.deri.org/2008/07/n-quads/).

Through other gems, more readers and writers are implemented, including:

  * [JSON-LD](http://json-ld.org/) using the [JSON::LD](http://gkellogg.github.com/json-ld) gem.
  * [Microdata](http://en.wikipedia.org/wiki/Microdata_(HTML)) using the [RDF::Microdata](http://ruby-rdf.github.com/rdf-microdata) gem.
  * [Notation3](http://en.wikipedia.org/wiki/Notation3) using the [RDF::N3](http://ruby-rdf.github.com/rdf-n3) gem.
  * [RDF/JSON](http://n2.talis.com/wiki/RDF_JSON_Specification) using the [RDF::JSON](http://ruby-rdf.github.com/rdf-json) gem.
  * [RDF/XML](http://en.wikipedia.org/wiki/RDF/XML) using the [RDF::RDFXML](http://ruby-rdf.github.com/rdf-rdfxml) gem.
  * [RDFa](http://en.wikipedia.org/wiki/RDFa) using the [RDF::RDFa](http://ruby-rdf.github.com/rdf-rdfa) gem.
  * [TriG](http://wifo5-03.informatik.uni-mannheim.de/bizer/trig/) using the [RDF::TriG](http://ruby-rdf.github.com/rdf-trig) gem.
  * [TriX](http://en.wikipedia.org/wiki/TriX_(syntax)) using the [RDF::TriX](http://ruby-rdf.github.com/rdf-trix) gem.
  * [Turtle](http://www.w3.org/TR/turtle/) using the [RDF::Turtle](http://ruby-rdf.github.com/rdf-turtle) gem.

Additional readers and writers are available through [Redland](http://librdf.org) [Raptor](http://librdf.org/raptor/) bindings using the [RDF::Raptor](http://ruby-rdf.github.com/rdf-raptor) gem.

In addition to native support for BGP-based queries, there is a full-conferment [SPARQL](http://en.wikipedia.org/wiki/Sparql) 1.0 gem ([SPARQL gem](http://ruby-rdf.github.com/sparql)), and SPARQL 1.1 client gem ([SPARQL::Client](http://ruby-rdf.github.com/sparql-client)).

All of these gems can be packaged together using the [Linkeddata](http://ruby-rdf.github.com/linkeddata), [Rack::LinkedData](http://ruby-rdf.github.com/rack-linkeddata), and [Sinatra::LinkedData](http://ruby-rdf.github.com/sinatra-linkeddata) gems.

There are also a number of storage adaptors for popular backends, including the following:

  * [MongoDB](http://www.mongodb.org) using the [RDF::Mongo](https://github.com/ruby-rdf/rdf-mongo) gem.
  * [Sesame](http://www.openrdf.org) using the [RDF::Sesame](https://github.com/ruby-rdf/rdf-sesame) gem.
  * [Virtuoso](http://virtuoso.openlinksw.com/dataspace/dav/wiki/Main/VOSRDF) using the [RDF::Virtuoso](https://github.com/digibib/rdf-virtuoso) gem.
  * [AllegroGraph](http://www.franz.com/agraph/allegrograph/) using the [RDF::AllegroGraph](https://github.com/ruby-rdf/rdf-agraph) gem.

## Background

I first became involved with RDF when working on the [Connected Media Experience](http://connectedmediaexperience.org) (CME) design (see [blog entry](http://greggkellogg.net/2011/05/07/cme-and-the-semantic-web)). Having designed a proprietary metadata standard for [Gracenote](http://www.gracenote.com) and [Warner Music Group](http://www.wmg.com) (later for CME), through review with [Lucas Gonze](http://gonze.com/blog/), I was introduced to the [Music Ontology](http://musicontology.com), which had done an extremely thorough modeling of the music domain. This caused significant debate in the CME community, which lead to an updated design based on RDF, and many of the ideas from the [Music Ontology](http://musicontology.com).

During the same review, I was also introduced to [RDFa](http://en.wikipedia.org/wiki/RDFa) as a mechanism of embedding music metadata within a web page. Since CME is endeavoring to create a standard for enhanced digital media packages, [HTML5](http://www.w3.org/TR/html5/) and [RDFa](http://en.wikipedia.org/wiki/RDFa) are natural technologies to utilize.

My methodology for approaching architectures and specifications is based on parallel prototyping, to validate the details of a design, and sometimes serve as the bases for an implementation. For several years (starting in 2007) I had been using [Ruby on Rails](http://rubyonrails.org) and was quite invested in the [Ruby](http://www.ruby-lang.org/en/). My first attempts to integrate [RDFa](http://en.wikipedia.org/wiki/RDFa) into the implementation made use of the [Raptor](http://librdf.org/raptor/) parser, which unfortunately was not up-to-date with respect to the [RDFa](http://en.wikipedia.org/wiki/RDFa) 1.0 specification current at the time. Additionally, I found that the Ruby Bindings suffered from memory leaks, and went to look for a native Ruby implementation. This lead to [Tom Morris](http://tommorris.org/)' [Reddy](https://github.com/tommorris/reddy) gem. This was a port of the Python [rdflib](http://code.google.com/p/rdflib/) package to Ruby, which was going in the right direction, but had fallen into disuse. I created my own fork, and later released as [RdfContext](https://github.com/gkellogg/rdf_context) which had complete implementations for [RDF/XML](http://en.wikipedia.org/wiki/RDF/XML), [RDFa](http://en.wikipedia.org/wiki/RDFa) 1.0, and [Notation3](http://en.wikipedia.org/wiki/Notation3) (N3-rdf level).

In the mean time, [Arto Bendiken](http://ar.to) and [Ben Lavender](http://bhuga.net) had been working on [RDF.rb](http://ruby-rdf.github.com/rdf), taking a different approach closer to [Sesame](http://www.openrdf.org). The design of [RDF.rb](http://ruby-rdf.github.com/rdf) is quite elegant, making effective use of natural Ruby idioms, and taking an approach based heavily on using Ruby module extensions. After a prod by [Nick Humfrey](http://www.aelius.com/njh/) who had started a port of my [RDF/XML](http://en.wikipedia.org/wiki/RDF/XML) parser, I jumped in and ported the bulk of my RDF parsers and serializers, eventually adding several more, to make the [RDF.rb](http://ruby-rdf.github.com/rdf) platform one of the most complete in terms of standards support across all major (and minor) serialization formats. In 2010, I was asked to join the [RDF.rb](http://ruby-rdf.github.com/rdf) core development team, and soon became the primary maintainer after Arto and Ben became fully committed with [Dydra](http://dydra.com/).

Arto and I finally got together recently to move all of the gems primary repositories to the [Ruby RDF](http://ruby-rdf.github.com) organization on GitHub, and release them as 1.0. The next significant release should be 1.1, to coincide with the release of the [RDF 1.1](http://www.w3.org/TR/rdf11-concepts/) specs from the W3C.
---
id: 146
title: RDF::RDFa update with vocabulary expansion, RDF collections and more
date: 2011-09-06T06:37:48+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=146
permalink: /2011/09/rdfrdfa-update-with-vocabulary-expansion-rdf-collections-and-more/
categories:
  - Ruby
  - Semantic Web
---
I've updated [RDF::RDFa](http://rubygems.org/gems/rdf-rdfa) with updates from recent changes to RDF Core:

  * Deprecate explicit use of @profile
  * Add rdfa:hasVocabulary when encountering @vocab
  * Implemented Reader#expand to perform vocabulary expansion using RDFS rules 5, 7, 9 and 11.

Additionally, experimental support for RDF Collections (lists) has been added, based on [RDF Webapps working group Wiki notes](http://www.w3.org/2010/02/rdfa/wiki/Lists).

#### Remove RDFa Profiles

RDFa Profiles were a mechanism added to allow groups of terms and prefixes to be defined in an external resource and loaded to affect the processing of an RDFa document. This introduced a problem for some implementations needing to perform a cross-origin GET in order to retrieve the profiles. The working group elected to drop support for user-defined RDFa Profiles (the default profiles defined by RDFa Core and host languages still apply) and replace it with an inference regime using vocabularies. Parsing of @profile has been removed from this version.

#### Vocabulary Expansion

One of the issues with vocabularies was that they discourage re-use of existing vocabularies when terms from several vocabularies are used at the same time. As it is common (encouraged) for RDF vocabularies to form sub-class and/or sub-property relationships with well defined vocabularies, the RDFa vocabulary expansion mechanism takes advantage of this.

As an optional part of RDFa processing, an RDFa processor will perform limited [RDFS entailment](http://www.w3.org/TR/rdf-mt/#rules), specifically rules rdfs5, 7, 9 and 11. This causes sub-classes and sub-properties of type and property IRIs to be added to the output graph.

RDF::RDFa::Reader implements this using the `#expand` method, which looks for `rdfa:hasVocabulary` properties within the output graph and performs such expansion. See an example in the usage section.

#### RDF Collections (lists)

One significant RDF feature missing from RDFa was support for ordered collections, or lists. RDF supports this with special properties `rdf:first`, `rdf:rest`, and `rdf:nil`, but other RDF languages have first-class support for this concept. For example, in [Turtle](http://www.w3.org/TR/2011/WD-turtle-20110809/), a list can be defined as follows:

    [ a schema:MusicPlayList;
      schema:name "Classic Rock Playlist";
      schema:numTracks 5;
      schema:tracks (
        [ a schema:MusicRecording; schema:name "Sweet Home Alabama";       schema:byArtist "Lynard Skynard"]
        [ a schema:MusicRecording; schema:name "Shook you all Night Long"; schema:byArtist "AC/DC"]
        [ a schema:MusicRecording; schema:name "Sharp Dressed Man";        schema:byArtist "ZZ Top"]
        [ a schema:MusicRecording; schema:name "Old Time Rock and Roll";   schema:byArtist "Bob Seger"]
        [ a schema:MusicRecording; schema:name "Hurt So Good";             schema:byArtist "John Cougar"]
      )
    ]
    

defines a playlist with an ordered set of tracks. RDFa adds the @member attribute, which is used to identify values (object or literal) that are to be placed in a list. The same playlist might be defined in RDFa as follows:

    <div vocab="http://schema.org/" typeof="MusicPlaylist">
      <span property="name">Classic Rock Playlist</span>
      <meta property="numTracks" content="5"/>
    
      <div rel="tracks" member="">
        <div typeof="MusicRecording">
          1.<span property="name">Sweet Home Alabama</span> -
          <span property="byArtist">Lynard Skynard</span>
         </div>
    
        <div typeof="MusicRecording">
          2.<span property="name">Shook you all Night Long</span> -
          <span property="byArtist">AC/DC</span>
        </div>
    
        <div typeof="MusicRecording">
          3.<span property="name">Sharp Dressed Man</span> -
          <span property="byArtist">ZZ Top</span>
        </div>
    
        <div typeof="MusicRecording">
          4.<span property="name">Old Time Rock and Roll</span>
          <span property="byArtist">Bob Seger</span>
        </div>
    
        <div typeof="MusicRecording">
          5.<span property="name">Hurt So Good</span>
          <span property="byArtist">John Cougar</span>
        </div>
      </div>
    </div>
    

This basically does the same thing, but places each track in an rdf:List in the defined order.

You can try both these and other RDF gems a the [distiller](http://rdf.greggkellogg.net/distiller/).
---
id: 140
title: Things people get wrong in RDFa markup
date: 2011-06-19T15:53:56+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=140
permalink: /2011/06/things-people-get-wrong-in-rdfa-markup/
categories:
  - Semantic Web
---
# Things people get wrong in RDFa markup

Lately, I’ve been looking a lot of both RDFa and Microdata formatted HTML. There are a number of things that authors (even experts) regularly get wrong:

## @src and @rel attributes create reverse relation

Having code such as the following:

    <img rel="image" src="image.jpg" />
    ...
    

You’d think that this would indicate that the icon for the document is

    <> xhv:image <image.jpg>
    

but it actually says:

    <image.jpg> xhv:image <> .
    

The why of this is lost in the haze of history, but people regularly get this wrong. To get what you need, consider something like the following markup:

    <span rel="image"><img src="image.jpg" /></span>
    ...
    

## @rel and @typeof and/or @about shouldn’t be on the same element

Another common mistake is format such as the following:

    <div rel="mainContentOfPage" about="#me" typeof="Person">
      <p>
        Name: <span property="name">Gregg Kellogg</span></p>
      <p>
        Knows: <a href="https://greggkellogg.net/#me" rel="knows">Myself</a></p>
    </div>
    

Placing `@rel` and `@about` or `@typeof` on the same element indicates that the `@about`/`@typeof` indicate the subject not the object of a relation. To get the desired effect, use `@resource` (or `@href`), however, this does not let you set the type of the object resource. Alternatively, use the following type of markup:

    <div rel="mainContentOfPage">
      <div about="#me" typeof="Person">
        <p>
          Name: <span property="name">Gregg Kellogg</span></p>
        <p>
          Knows: <a href="https://greggkellogg.net/#me" rel="knows">Myself</a>
        </p>
      </div>
    </div>
    

Another area of common mis-understanding is that the document order of statements within an HTML document is not significant when creating a list of resources. Consider the following example from schema.org/MusicPlaylist:

    <div itemscope="" itemtype="http://schema.org/MusicPlaylist">
      <span itemprop="name">Classic Rock Playlist</span>
      <div itemprop="tracks" itemscope="" itemtype="http://schema.org/MusicRecording">
        1. <span itemprop="name">Sweet Home Alabama</span> - <span itemprop="byArtist">Lynard Skynard</span>
      </div>
      <div itemprop="tracks" itemscope="" itemtype="http://schema.org/MusicRecording">
        2. <span itemprop="name">Shook you all Night Long</span> - <span itemprop="byArtist">AC/DC</span>
      </div>
      ...
    </div>
    

You would think that this describes a track ordering, but it does not (at least in RDF). Doing this requires RDF List constructs missing from both Microdata and RDFa. In Turtle, you could do it as follows:

    @prefix: <http://schema.org> .
    [ a :MusicPlaylist;
      :name "Classic Rock Playlist";
      :numTracks 5;
      :tracks (
        [ a :MusicRecording; :name "Sweet Home Alabama"; :byArtist "Lynard Skynard"]
        [a :MusicRecording; :name "Shook you all Night Long"; :byArtist "AC/DC"]
        ...
      )
    ]
    

It would seem obvious that an HTML ordered list could be used to generate an RDF List, but it received to achieve enough interest to make it through.

These are just a couple of things that are confusing about RDFa, and offer good fodder for Microdata proponents to complain about the complexity of RDFa markup. It’s important to note that a core goal of [RDFa 1.1](http://www.w3.org/TR/rdfa-core/) is to be compatible with RDFa 1.0 (RDFa in XHTML), in which these decisions were established. Perhaps a reconciliation between Microdata and RDFa could take the best of both:

  * Craft RDF friendly URIs from terms (such as schema:Person above),
  * Reduce amount of document structure needed to describe common use cases,
  * Better intuitive generation of RDF output,
  * Ability to avoid RDF generation and go straight to JSON (perhaps JSON-LD),
  * Use common URI prefixes,
  * RDF Lists,
  * Promote better HTML readability.

That’s my 2 cents (for now)

## Update

The RDFa Working Group recently decided to change the behavior of @src in RDFa Core 1.1 to be the same as @href. This means that

    <img rel="image" src="image.jpg" />
    ...
    

Actually does now generate the following:

    <> xhv:image <image.jpg>
    

Recent updates to Microdata to RDF processing now do place multiple items in a list, but this is subject to further specification.

In RDFa, this can now be done with the @inlist attribute, which places values in an RDF Collection (rdf:List).

    <div vocab="http://schema.org/" typeof="MusicPlaylist">
      <span property="name">Classic Rock Playlist</span>
      <div rel="tracks" inlist="">
        1. <div typeof="MusicRecording">
              <span property="name">Sweet Home Alabama</span> - <span property="byArtist">Lynard Skynard</span>
      </div>
      2. <div typeof="MusicRecording">
            <span property="name">Shook you all Night Long</span> - <span property="byArtist">AC/DC</span>
      </div>
      ...
    </div>
    

Now generates the following Turtle:

    @prefix: <http://schema.org> .
    [ a :MusicPlaylist;
      :name "Classic Rock Playlist";
      :tracks (
        [ a :MusicRecording; :name "Sweet Home Alabama"; :byArtist "Lynard Skynard"]
        [ a :MusicRecording; :name "Shook you all Night Long"; :byArtist "AC/DC"]
        ...
      )
    ]
---
id: 116
title: CME and the Semantic Web
date: 2011-05-07T01:21:03+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=116
permalink: /cme-semweb
custom_permalink:
  - cme-semweb
categories:
  - Media
  - Semantic Web
---
# CME and the Semantic Web

## Introduction

The [Connected Media Experience](http://connectedmediaexperience.org) is a consortium formed to promote technical standards for enhanced digital media packages such as music, movie, television and eBook releases. It’s origins go back to 2007 and was conceived as a platform for providing a rich experience for enjoying media across a variety of devices. Early on, the importance of rich semantic information to inform and enhance an _Experience_ was recognized as an important differentiator. CME is intended to include social aspects allowing users to connect and interact with each other, so a rich means of identifying and describing elements of a release, and the release itself, is an important design consideration.

During the course of development, it became clear that The Semantic Web community, had developed important technology for realizing a system such as [CME](http://connectedmediaexperience.org). The group was introduced to the [Music Ontology](http://musicontology.com/) and [RDFa](http://www.w3.org/2010/02/rdfa/sources/rdfa-primer/) in defining and, along with [HTML5](http://www.w3.org/TR/html5/), [CSS3](http://www.w3.org/TR/css3-roadmap/) and [JavaScript](http://en.wikipedia.org/wiki/JavaScript), useful in creating portable presentations of these releases.

The Music Industry has also developed rich meta-data release descriptions in the form of [DDEX](http://www.ddex.net/ "Digital Data Exchange") (Digital Data Exchange). However, DDEX’ goals are focused on music distribution for business-to-business transactions, rather than to suit the needs of the consumer.

Ultimately, the group decided against including Semantic Web technologies to describe rich releases, in favor of a publisher-friendly model based on [HTML5](http://www.w3.org/TR/html5/) and [Widgets](http://www.w3.org/standards/techs/widgets#w3c_all) to achieve presentational objectives, without the rich semantic representational component. (The format does include limited semantic data in a proprietary format). This note attempts to discuss some of the reasons behind this decision, and lessons that the Semantic Web community might learn from the experience.

## Background

In 2007, the author was contracted by [Gracenote](http://gracenote.com/) and [Warner Music Group](http://wmg.com/ "Warner Music Group") to help develop use cases, demonstrations and a high level architecture for a _Connected Media Experience_ (CMX, as it was known at the time). Early demos made use of Adobe [Flash](http://en.wikipedia.org/wiki/Adobe_Flash "Adobe Flash") and [Flex](http://en.wikipedia.org/wiki/Adobe_Flex "Adobe Flex") technologies to create compelling experiences on mobile and desktop platforms, and a relational metadata representation using a proprietary (ad-hoc) XML schema. However, this was found to cause many interoperability problems and was ultimately abandoned in favor of open technologies.

As CME progressed, other major music labels including [Universal Music Group](http://www.universalmusic.com/) and [Sony Music](http://www.sonymusic.com/) (then Sony BMG) joined to create the [Connected Media Experience](http://connectedmediaexperience.org) Standards Setting Organization for furthering development of the specification and to solicit contributions from other interested industry members to promote such a standard. (The author served as Chairman of the Technical Working Group until March of this year.)

The [Music Ontology](http://musicontology.com/) was introduced as a rich metadata format using [RDF](http://www.w3.org/RDF/) and [OWL](http://www.w3.org/TR/owl2-overview/) to describe music releases and content. It is based on [Friend-of-a-Friend (FOAF)](http://en.wikipedia.org/wiki/FOAF_(software)) and [Functional Requirements of Bibliographic Records (FRBR)](http://en.wikipedia.org/wiki/Functional_Requirements_for_Bibliographic_Records) to describe albums, tracks, contributors, musical works, performances and releases. The needs of describing releases beyond music ultimately drove CME to create their own Ontology taking elements from [FOAF](http://en.wikipedia.org/wiki/FOAF_(software)), [FRBR](http://en.wikipedia.org/wiki/Functional_Requirements_for_Bibliographic_Records), [Music Ontology](http://musicontology.com/), and [DDEX](http://www.ddex.net/ "Digital Data Exchange").

For some time, package presentation was considered as a disaggregated release, including individual audio and video tracks combined with a _Manifest_ (specified in RDF/XML using the CME Ontology) to create different user interfaces depending on the capabilities of the particular target devices. With the rise of smart-phones and HTML browsers within other consumer electronics devices, this was eventually changed to an HTML5+RDFa manifest, which could service as both a release description and a presentation, when used in combination with [JavaScript](http://en.wikipedia.org/wiki/JavaScript) and [CSS3](http://www.w3.org/TR/css3-roadmap/).

![CME Entity Relationship Diagram](/galleries/CME/ERD.png "CME Entity Relationship Diagram")

Throughout the course of development, CME members had difficulty in accepting the advantages semantic technologies being used, which led to low participation and lack of involvement in generating the specifications. Fundamentally, the difficulty of working with the technologies led the group to abandon a rich semantic representation of a release and settle on more established web technologies and proprietary metadata formats.

## Examples

The basic idea of the CME Vocabulary was to allow a simple hierarchal representation of Work/Production/Signal/Manifestation with relationship to a Release/Collection.

Given a particular encoding, say of Hoagy Carmichael’s “Stardust”, a simple Manifestation might be described as follows:

    [ a cme:Encoding, mo:MusicalManifestation;
    dc:title "Stardust"@en-us;
    dc:format "audio/mpeg"^^dc:MediaType;
    cme:duration "PT3M53S"^^xsd:duration;
    dc:issued "1978-04-01"^^xsd:date
    ] .
    

As this represents a specific formatted manifestation of a recorded signal, we can add more information:

    <#stardust> a cme:Audio, mo:Signal;
    dc:title "Stardust"@en-us;
    cme:displayArtist <http://dbpedia.org/data/Willie_Nelson>;
    cme:lyrics <http://www.metrolyrics.com/stardust-lyrics-willie-nelson.html>;
    mo:isrc "XX-XXX-XX-00000"^^cme:ISRCType;
    mo:label <http://dbpedia.org/data/Columbia_Records>;
    cme:encoding [ a cme:Encoding, mo:MusicalExpression;
      dc:title "Stardust"@en-us;
      dc:format "audio/mpeg"^^dc:MediaType;
      cme:duration "PT3M53S"^^xsd:duration;
      dc:issued "1978-04-01"^^xsd:date
    ] .
    

However, there’s more we can say about this recording, for instance, that it was recorded at a particular time with various performers:

    [ a mo:Performance;
    dc:title "Studio recording of Stardust"@en-us;
    dc:created "1977-12-12"^^dc:date;
    mo:producer <http://dbpedia.org/data/Booker_T._Jones>;
    mo:singer <http://dbpedia.org/data/Willie_Nelson>;
    mo:performer <http://dbpedia.org/data/Chris_Ethridge>,
      <http://dbpedia.org/data/Paul_English>,
      <http://dbpedia.org/data/Booker_T._Jones>;
    cme:expression <#stardust>
    ] .
    <#stardust> a cme:Audio, mo:Signal .
    

We also know that Hoagy Charmichael composed the song “Stardust”

    <http://dbpedia.org/data/Stardust_(song)> a mo:MusicalWork
    dc:title "Stardust"@en-us;
    mo:composer <http://dbpedia.org/data/Hoagy_Carmichael>;
    db:created "1927-10-31"^^xsd:date
    mo:performed_in [ a mo:Performance;
      dc:title "Studio recording of Stardust"@en-us;
      dc:created "1977-12-12"^^dc:date;
      mo:producer <http://dbpedia.org/data/Booker_T._Jones>;
      mo:singer <http://dbpedia.org/data/Willie_Nelson>;
      mo:performer <http://dbpedia.org/data/Chris_Ethridge>,
        <http://dbpedia.org/data/Paul_English>,
        <http://dbpedia.org/data/Booker_T._Jones>;
      cme:expression <#stardust>
    ] .
    <#stardust> a cme:Audio, mo:Signal ....
    

We could continue this to describe multiple performances, expressions or encodings.

A particular encoding might appear on many different albums or playlists, so we can’t encode information such as track number with the `cme:Audio`. This is encoded in a _Collection_ contained within an _Release_:

    <> a cme:PrimaryRelease;
    owl:seeAlso <http://dbpedia.org/data/Stardust_(album)>;
    dc:title "Stardust"@en-us;
    cme:displayArtist <http://dbpedia.org/data/Willie_Nelson>;
    cme:parentalWarning "unspecified"^^cme:ParentalWarningType;
    mo:grid "A1-a1788-aaaaaaaaaa-b"^^cme:GRid;
    cme:presentation <js/authored.js>;
    cme:audioCollection [ a cme:AudioCollection;
      dc:title "Songs"@en-us;
      cme:item [ a cme:Item;
        cme:itemNumber "1";
        cme:expression <#stardust>
      ]
    ] .
    <http://dbpedia.org/data/Stardust_(song)> a mo:MusicalWork ....
    <#stardust> a cme:Audio, mo:Signal ....
    

There is much more that can be said about an album, including links to reviews, alternate performances, videos, photos and so forth. RDF provides an expressive mechanism for describing such rich metadata.

### Discovery

RDF is based strongly on the notion of universal resource identifiers to identify particular resources or concepts. Using, the so-called _follow your nose_ principle, a specific agent might use identifiers contained within a release to discover more information about a particular subject; for instance reviews of the album stored on DBPedia or elsewhere.

### Extensibility

As a release described as an RDF Graph, using the principle of “Anyone can say anything about anything”, additional information can be authored about a given release. This might be useful for adding premium content such as extra audio tracks, music videos or concert photos. Moreover, consumers may choose to use a CME release as a creative starting point by creating alternate user interface skins, personal pictures or anything else they might be interested in; this is one aspect of the _Connected_ aspect of The _Connected Media Experience_.

### Social Web

Giving CME release elements URIs allows them to be used for other social activities, such as [Activity Streams](http://activitystrea.ms/), Facebook “Like” operations, or other mechanisms.

## Demise of CME Semantic Releases

In many ways, the music industry is not ready for many of the open aspects of an RDF format; the concept of using existing universal identifiers (such as DBPedia URIs) that they do not directly control can be a barrier, and they are not yet prepared to maintain their own publicly available repository of unique identifiers representing their artists, musical works and releases.

Artists are naturally concerned that their product is presented in a manner consistent with their original design intentions. Understandably, they want to insure that their intellectual product is portrayed as intended. However, this desire can come in conflict with the read/write web where fans often make use of authored material in mashups and other derived works. Coming to a reasonable understanding of fair use, and how this can be moderated remains an important challenge.

The industry has made great strides is in improving their use of ISRC identifiers, which in the past were not always reliable. [ISRC](http://www.ifpi.org/content/section_resources/isrc.html), along with [GRid](http://www.ifpi.org/content/section_resources/grid.html), [ISWC](http://www.iswc.org) and [ISNI](See%20http://www.isni.org/) identifiers can be useful in differentiating resources, and typically cannot be dereferenced. They are also not URNs, so they’re not appropriate to form an owl:sameAs relationship with, for example, DBPedia URIs.

The complexity of authoring packages using RDF formats given the lack of well curated metadata was a large complicating factor in CME moving away from an RDF representation. But equally daunting was the complexity of authoring packages using an RDFa description of the package. As originally conceived, much of a package presentation could be based on the raw HTML5+RDfa description of a release. CSS and JavaScript are extremely capable of creating amazing user interfaces. However, the general capability of web designers, as well as the complexity of authoring valid packages, really gets in the way of this. Until tools emerge that allow for the simple authoring of semantically-rich dynamic HTML5 presentations, this will likely remain an opportunity for future music publishers.

It’s important to note that nothing in CME excludes RDF and a rich set of metadata, and we may yet see CME releases that use the original design principles to achieve similar objectives. What won’t be there is a base-level of metadata in every CME release that platforms can depend upon for extending the basic experience.

## Lessons for the Music Industry

The concept of rich music (media) releases in an era of pervasive access to free content is an ongoing issue for the music industry. CME is an attempt to provide consumers a reason to own their media, rather that obtain it alternatively. Providing rich curated data about subjects of interest to consumers can be one way in which future exists for content owners who legitimately need to profit from their artistry.

Giving up control about information, including the presentation of artistic works, is a barrier for music publishers. Existing contractual obligations do not necessarily align with the expectations of consumers.

Certainly, the web is full of bad data, and relying on an external service which does not provide content owners a reliable way of ensuring that data is essential. Even getting a handle on their own internal use of identifiers, for instance having a single identifier to describe performers on different releases, much less on performance that cross label boundaries, is a big challenge for legacy systems that were not intended to be used for curating publicly available information.

The major labels do work with metadata services to provide accurate information, and many retailers use these 3rd party information services, along with proprietary identifiers to provide consumers with limited metadata about music releases. For 3rd party information services, there is a cost to maintaining quality metadata, which often means that reliable information remains behind pay walls.

As mentioned above, where there is a large amount of metadata available through various open data sources, it is often of poor quality. Finding a balance of allowing for curation of such data by content owners is an important step in bringing about reliable rich metadata. To some, the lesson here is not one of control, but one of clean-up and publish: “if you don’t give them what they want, they’ll get it from someplace else”.

The very lack of quality metadata about musical releases from the major labels is responsible for the rise of several services that provide such information, for example [Gracenote](http://gracenote.com/), [AMG](http://www.allmusic.com/), [MusicBrainz](http://musicbrainz.org/) and [FreeDB](http://www.freedb.org/). Providing curated information about music releases in standardized RDF formats is a potential business opportunity for such companies.

## Lessons for the Semantic Web Community

RDF was founded by academics to be logically consistent and rich. To a large degree, it continues to be dominated by academic interests. This has led to a rich and consistent representational format with very well thought out elements (e.g., entailment, inference, semantic equivalence, etc.). However, the pace of change can be slow and outreach to the open web community is not necessarily a priority.

There is a fair appreciation within the major music labels of the value and promise of RDF as a means of providing rich metadata. The fact is, though, that proprietary metadata formats are much simpler to implement and manage. According to a key opinion maker: “I bet the average developer can get a simple XML-based music metadata system up and running in less time than it would take to read the Music Ontology document. We can get most (all?) of the benefits of RDF through simpler means.” To be fair, closed world systems are easier to implement and manage; a goal of RDF is to allow for datasets to be shared and mixed, doing so requires shared vocabularies and representations.

RDFa is one example of an RDF technology that came from the open web community and has had astounding uptake. It is estimated that ~4% of all web content now includes some amount of RDFa description [1](http://connectedmediaexperience.org). But, other areas in need of standardization (e.g., JSON RDF representation) remain mired in controversy and/or apathy.

Given HTML’s strong support for lists (e.g., `ol`, `ul`, `dl`, …), it is amazing that RDFa has no basic markup support for RDF lists. Even if it did, RDF lists are based on linked-lists, rather than flat collections. This makes it almost impossible to query an RDF graph to determine the constituent elements of a container such as a playlist or album without using higher-level semantic constructs (see [Ordered List Ontology](http://smiy.sourceforge.net/olo/spec/orderedlistontology.html)).

1: <http://tripletalk.wordpress.com/2011/01/25/rdfa-deployment-across-the-web/>

<div class="meta">
  <p>
    <small>Published on <span class="typo_date date gmttimestamp-1304731260" title="Sat, 07 May 2011 01:21:00 GMT">Sat, 07 May 2011 01:21:00 GMT</span> under <a href="https://greggkellogg.net/category/media" rel="tag">Media</a>, <a href="https://greggkellogg.net/category/rdf" rel="tag">Semantic Web</a>. </small>
  </p>
</div>

## If you liked this article you can [add me to Twitter](http://twitter.com/@gkellogg) {#twitter}

### [12 comments](https://greggkellogg.net/2011/05/07/cme-and-the-semantic-web)  <a href="#commentform" ref="nofollow">»</a>
  
 {#commentaires.feedback}

<ul id="commentList" class="unstyled">
  <li id="comment-194">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=96aa135ef89c4aa844317658aa878e01&#038;size=48&#038;url=http://smiy.org" class="avatar gravatar" /> </p> <h4>
      By <a href="http://smiy.org" rel="nofollow">zazi</a> <span class="typo_date date gmttimestamp-1304625114" title="Thu, 05 May 2011 19:51:54 GMT">Thu, 05 May 2011 19:51:54 GMT</span><br />
    </h4>
    
    <p>
      I bet the average developer will run into the desaster of a more difficult data cleaning task with that simple XML-based music metadata system. <br /> Semantic Web technology-powered systems have higher initial deployment costs, but they are durable like the Web itself. So in the long term Semantic Web technologies will win!<br /> We cannot really make the things simpler as they are in reality. The description of music can be quite complex. See, e.g., <a href="http://wiki.musicontology.com/index.php/Mo_-_levels_of_abstraction_-_whole_way_down" rel="nofollow">this partial description of an album of the Beatles</a>. So we have to respect that complexity if we like to represent this knowledge.
    </p>
    
    <hr />
  </li>
  <li id="comment-195">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=6a7c007a98b09397a8a6c9641e38efb0&#038;size=48&#038;url=http://johnwright.me" class="avatar gravatar" /> </p> <h4>
      By <a href="http://johnwright.me" rel="nofollow">John Wright</a> <span class="typo_date date gmttimestamp-1304643124" title="Fri, 06 May 2011 00:52:04 GMT">Fri, 06 May 2011 00:52:04 GMT</span><br />
    </h4>
    
    <p>
      Very good and informative case study on RDF/Semantic Web application/adoption (or rather the lack of it). Thanks!
    </p>
    
    <hr />
  </li>
  <li id="comment-197">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=d41d8cd98f00b204e9800998ecf8427e&#038;size=48&#038;url=" class="avatar gravatar" /> </p> <h4>
      By Gregg Kellogg <span class="typo_date date gmttimestamp-1304650045" title="Fri, 06 May 2011 02:47:25 GMT">Fri, 06 May 2011 02:47:25 GMT</span><br />
    </h4>
    
    <p>
      Clearly, I really believe that the long-term benefits of using good semantic markup are worth it, and many in the music industry do as well. We may not see this as a standard base-level markup for music releases, but I believe we will see semantically rich music, video and ebook content before too long.
    </p>
    
    <hr />
  </li>
  <li id="comment-198">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=576c9e5ccc53742421fad6b0304422b3&#038;size=48&#038;url=http://infteam.jiscinvolve.org" class="avatar gravatar" /> </p> <h4>
      By <a href="http://infteam.jiscinvolve.org" rel="nofollow">David F. Flanders</a> <span class="typo_date date gmttimestamp-1304668361" title="Fri, 06 May 2011 07:52:41 GMT">Fri, 06 May 2011 07:52:41 GMT</span><br />
    </h4>
    
    <p>
      So what do you make of central stores of data like #linkedbrainz that provides a standarised store of RDFa metadata for use, is that helpful or usable to any of the above mentioned stakholders. Thanks for article, very interesting.
    </p>
    
    <hr />
  </li>
  <li id="comment-199">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=a7657471c231304d1d7e0d5144ee8200&#038;size=48&#038;url=http://hsivonen.iki.fi/" class="avatar gravatar" /> </p> <h4>
      By <a href="http://hsivonen.iki.fi/" rel="nofollow">Henri Sivonen</a> <span class="typo_date date gmttimestamp-1304669438" title="Fri, 06 May 2011 08:10:38 GMT">Fri, 06 May 2011 08:10:38 GMT</span><br />
    </h4>
    
    <p>
      Thank you for taking the time to write this.
    </p>
    
    <p>
      One comment about a bit that makes things look better than they are:
    </p>
    
    <blockquote>
      <p>
        RDFa is one example of an RDF technology that came from the open web community and has had astounding uptake. It is estimated that ~4% of all web content now includes some amount of RDFa description
      </p>
    </blockquote>
    
    <p>
      This is rather misleading. As pointed out by your source, this is largely attributable to sites wanting to integrate with three particular big sites: Yahoo!, Google and Facebook. The Yahoo! bit (SearchMonkey) has already been discontinued. Google and Facebook don't actually implement RDFa processing as specified even though they use syntax that looks like RDFa. This makes it appear that something standards-based is going on even though for all practical purposes, what's happening is that sites are using Google's service-specific markup and Facebook's service-specific markup to be ingested by Google-written code or Facebook-written code&#8211;not by serendipitous Semantic Web agents that implement RDFa as specced.
    </p>
    
    <hr />
  </li>
  <li id="comment-200">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=d41d8cd98f00b204e9800998ecf8427e&#038;size=48&#038;url=" class="avatar gravatar" /> </p> <h4>
      By mhepp@computer.org <span class="typo_date date gmttimestamp-1304675589" title="Fri, 06 May 2011 09:53:09 GMT">Fri, 06 May 2011 09:53:09 GMT</span><br />
    </h4>
    
    <p>
      Authoring RDFa patterns looses a lot complexity if you
    </p>
    
    <ol>
      <li>
        start modeling in Turtle syntax (<a href="http://www.w3.org/TeamSubmission/turtle/" rel="nofollow">http://www.w3.org/TeamSubmission/turtle/</a>)
      </li>
    </ol>
    
    <p>
      and
    </p>
    
    <ol>
      <li>
        translate that into an RDFa snippet via the tool:
      </li>
    </ol>
    
    <p>
      <a href="http://www.ebusiness-unibw.org/tools/rdf2rdfa/" rel="nofollow">http://www.ebusiness-unibw.org/tools/rdf2rdfa/</a>
    </p>
    
    <p>
      Many advocats of RDFa don't realize that the textbook-style of teaching RDFa, closely entangled with visible content, add a lot of unnecessary complexity.
    </p>
    
    <p>
      Martin
    </p>
    
    <hr />
  </li>
  <li id="comment-201">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=d41d8cd98f00b204e9800998ecf8427e&#038;size=48&#038;url=" class="avatar gravatar" /> </p> <h4>
      By Barry Norton <span class="typo_date date gmttimestamp-1304689494" title="Fri, 06 May 2011 13:44:54 GMT">Fri, 06 May 2011 13:44:54 GMT</span><br />
    </h4>
    
    <p>
      Thanks for the very interesting article.
    </p>
    
    <p>
      Just on one minor technical point: surely the problem with querying for membership of RDF lists, rather than sequences, is addressed by path expressions in SPARQL 1.1? I.e., It's not a representational problem, but a problem of the richness of the query language.
    </p>
    
    <p>
      (Of course I agree on the wish for better support in RDFa, as per Turtle/n3)
    </p>
    
    <hr />
  </li>
  <li id="comment-202">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=d41d8cd98f00b204e9800998ecf8427e&#038;size=48&#038;url=" class="avatar gravatar" /> </p> <h4>
      By Gregg Kellogg <span class="typo_date date gmttimestamp-1304700938" title="Fri, 06 May 2011 16:55:38 GMT">Fri, 06 May 2011 16:55:38 GMT</span><br />
    </h4>
    
    <p>
      @david, as I note, the majors are concerned about the quality of information. Linkedbrainz doesn't really address the quality issue, at least as far as the labels are concerned.
    </p>
    
    <p>
      I think having this data available in RDFa is great, but principally because it allows developers to find it and make use of it through mechanisms such as the forthcoming RDF API.
    </p>
    
    <hr />
  </li>
  <li id="comment-203">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=d41d8cd98f00b204e9800998ecf8427e&#038;size=48&#038;url=" class="avatar gravatar" /> </p> <h4>
      By Gregg Kellogg <span class="typo_date date gmttimestamp-1304701214" title="Fri, 06 May 2011 17:00:14 GMT">Fri, 06 May 2011 17:00:14 GMT</span><br />
    </h4>
    
    <p>
      @henry this is how standards come about (in the best circumstances) existing behavior is normalized and standardized. The fact that search engines find value in semantic markup makes it more likely people will use it to get noticed. Inevitably, there will be a move to make this data more uniform. Where service-specific markup has a problem is in the limited vocabularies they will process, assuming the values of well-known prefixes. Hopefully, this will be sorted out.
    </p>
    
    <hr />
  </li>
  <li id="comment-204">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=d41d8cd98f00b204e9800998ecf8427e&#038;size=48&#038;url=" class="avatar gravatar" /> </p> <h4>
      By Gregg Kellogg <span class="typo_date date gmttimestamp-1304701402" title="Fri, 06 May 2011 17:03:22 GMT">Fri, 06 May 2011 17:03:22 GMT</span><br />
    </h4>
    
    <p>
      @mhepp couldn't agree more. Note my own RDF-RDFa serializer: <a href="http://rdf.greggkellogg.net/distiller" rel="nofollow">http://rdf.greggkellogg.net/distiller</a>. Anything beyond insertion of pre-formatted snippets requires algorithmic HTML+RDFa serialization. Thus the importance of being able to act on such markup using CSS and/or jQuery. As long as HTML is hand-crafted, the use of markup such as RDFa will have limited impact; It's just too difficult to get it right.
    </p>
    
    <hr />
  </li>
  <li id="comment-205">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=d41d8cd98f00b204e9800998ecf8427e&#038;size=48&#038;url=" class="avatar gravatar" /> </p> <h4>
      By Gregg Kellogg <span class="typo_date date gmttimestamp-1304701500" title="Fri, 06 May 2011 17:05:00 GMT">Fri, 06 May 2011 17:05:00 GMT</span><br />
    </h4>
    
    <p>
      @barry I believe SPARQL 1.1 lists do create a syntax to allow for this, but the representation remains fairly expensive, at least in triple stores without built-in support for list entailment.
    </p>
    
    <hr />
  </li>
  <li id="comment-206">
    <img src="http://www.gravatar.com/avatar.php?gravatar_id=f8b6367d1abf0277c346d5273675b002&#038;size=48&#038;url=http://www.openlinksw.com/blog/~kidehen" class="avatar gravatar" /> </p> <h4>
      By <a href="http://www.openlinksw.com/blog/~kidehen" rel="nofollow">Kingsley Idehen</a> <span class="typo_date date gmttimestamp-1304704244" title="Fri, 06 May 2011 17:50:44 GMT">Fri, 06 May 2011 17:50:44 GMT</span><br />
    </h4>
    
    <p>
      Imagine this scenario.
    </p>
    
    <p>
      The issue had nothing to do with RDF or RDFa and more to do with structured data about music.
    </p>
    
    <p>
      Structured data would boil down to the following:
    </p>
    
    <ol>
      <li>
        <p>
          Every Data Object has a URI based Name
        </p>
      </li>
      
      <li>
        <p>
          Every Data Object has Representation &#8212; an Entity-Attribute-Value Graph
        </p>
      </li>
      
      <li>
        <p>
          Every Data Object's Representation was accessible from an Address (e.g. an HTTP URL).
        </p>
      </li>
    </ol>
    
    <p>
      Now based on the above, go look at any form of structured data produced by any of these music companies or broader Web 2.0 plays, and tell me if you don't see the very pattern outlined above modulo:
    </p>
    
    <ol>
      <li>
        <p>
          RDF or RDFa
        </p>
      </li>
      
      <li>
        <p>
          HTTP URI based Names &#8212; so most have a UUID or some other conventional unique identifier
        </p>
      </li>
      
      <li>
        <p>
          Entity-Attribute-Value based Graph for Object Representation.
        </p>
      </li>
    </ol>
    
    <p>
      The most important thing we need at this juncture is structured data that's accessible via Web Addresses.
    </p>
    
    <p>
      Until the opportunity costs of Linked Data become palpable to Music Industry decision makers, nothing will happen, and nothing should happen.
    </p>
    
    <p>
      What do I take from this post? Confirmation of practical reality 🙂
    </p>
    
    <hr />
  </li>
</ul>

<div class="well well-small">
  Comments are disabled
</div>

##### Trackbacks for _CME and the Semantic Web_
  
 {#trackbacks.feedback}
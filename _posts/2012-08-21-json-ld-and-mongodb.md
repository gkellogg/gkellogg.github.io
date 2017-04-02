---
id: 170
title: JSON-LD and MongoDB
date: 2012-08-21T13:50:56+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=170
permalink: /2012/08/json-ld-and-mongodb/
categories:
  - JavavScript
  - Ruby
  - Semantic Web
---
For the last several months, I&#8217;ve been engaged in an interesting project with [Wikia](http://wikia.com). Wikia hosts hundreds of thousands of special-interest wikis for things as varied as [pokemon](http://pikamon.wikia.com/wiki/Pokemon_Wiki), [best cellphone rate comparisons](http://prepaidwithdata.wikia.com/wiki/Prepaid_SIM_with_data), [TV shows](http://lost.wikia.com/wiki/Main_Page) and [Video Games](http://callofduty.wikia.com/wiki/Main_Page).

For those of you not aware of Wikia, it is an outgrowth of the [MediaWiki](http://www.mediawiki.org) and was founded by [Jimmy Wales](http://en.wikipedia.org/wiki/Jimmy_Wales) as a for-profit means of using the MediaWiki platform for exactly such interests.

Recently [MediaWiki Deutschland](http://www.wikimedia.de/wiki/Hauptseite) started work on [WikiData](http://meta.wikimedia.org/wiki/Wikidata), an effort to use Semantic Web principles to create a factual knowledge base that can be used within Wikis (typically to replace Infobox information, which can vary between different language versions). This is a somewhat different direction than [Semantic Media Wiki](http://semantic-mediawiki.org), which is more about using Wiki markup to express semantic relationships within a Wiki. As it happens [JSON-LD](http://json-ld.org/) is being considered as the data representation model for [WikiData]().

### Linked Data at Wikia

As it turns out, Wikia has been quite interested in leveraging these tools. I did mention that Wikia is a for-profit company; one way they do this is through in-page advertising, but the amount of knowledge curated by the hundreds of thousands of communities is staggering. Unfortunately, native Wiki markup just isn&#8217;t that semantic. However, much of the information represented is factual (at least within the world-view of the wiki community).

To that end, I&#8217;ve been working on an experiment using [JSON-LD]() and [MongoDB](http://www.mongodb.org) to power a parallel structured data representation of much of the information contained in a wiki. The idea is to add a minimal amount of markup (hopefully) to the Wiki text and templates so that information can be represented in the generated HTML using [RDFa](http://w3.org/TR/rdfa-core/). This allows the content of the Wiki to be mirrored in a [MongoDB]()-based service using [JSON-LD](). Once the data has been freed from the context of the limited Wiki markup, it can now be re-purposed outside of the Wiki itself.

### Knowledge modeling and data representation

Why use [RDFa]() and not [Microdata](http://www.w3.org/TR/microdata/)? The primary driver is the need to use multiple vocabularies to represent information. In my opinion, any new vocabulary needs to take into consideration [schema.org](http://schema.org/); [microdata](http://www.w3.org/TR/microdata/) works great with [schema.org](), and can generate [RDF]() (see [Microdata to RDF](http://www.w3.org/TR/microdata-rdf/)) as long as you&#8217;re constrained to a single vocabulary, don&#8217;t need to keep typed data, and don&#8217;t need to capture actual HTML markup. Unfortunately, any serious application beyond simple Search Engine Optimization (SEO) _does_ need to use these features. In our case, much of the interesting data to capture are fragments of the Wiki pages themselves. Moreover, the content of any Wiki, much less one that has as much special meaning as, say, a Video Game, needs to describe relationships that are not natively part of the [schema.org]() vocabulary. Schema does provide an [extension mechanism](http://schema.org/docs/extension.html) partly for this purpose, and recently the ability to tag subjects with an [additional type](http://semanticweb.com/schema-org-adds-additional-type-property_b30861), not part of the primary vocabulary (presumably [schema.org]()) was introduced. But, once the decision is made to use multiple vocabularies, RDFa has better mechanisms in place anyway.

At Wikia, we define a vocabulary as an extension to [schema.org](), that is, the classes defined within that vocabulary are sub-classes of [schema.org]() classes, although typically the properties are _not_ sub-properties of [schema.org]() properties (we may revisit this). For example, a `wikia:VideoGame` is a sub-class of `schema:CreativeWork`, and a `wikia:WikiText` is a sub-class of `schema:WebPageElement`. There are additional class and property definitions to describe the structural detail common to Video Games in describing characters, levels, weapons, and so forth. An [RDFa]() description will assert both the native class (e.g., `wikia:VideoGame`) and the [schema.org]() extension class (e.g. `schema:CreativeWork/VideoGame`). This allows search engines to make sense of the structured data, without the need to understand an externally defined vocabulary.

However, for Wikia&#8217;s purposes, and that of people wanting to work with in the Wikia structured-data echo-system, having a vocabulary that models the information contained within Wikia Wikis can be of great benefit. Key to this is knowning how much to model with classes and properties, and how much to leave to things such as naming conventions and keywords. In fact, there are likely cases where more per-wiki modeling is required, and we are continuing to explore ways in which we can further extend the vocabularies, without imposing a large burden on ontology development, and to keep the data reasonably generically useful.

### Linked Data API

Although RDFa structured in HTML can be quite useful as an API itself, modern Single Page Applications are better served through RESTful interfaces with a JSON representation. [JSON-LD]() was developed as a means of expressing Linked Data in JSON. It is fully compatible with [RDF](). Indeed, many of the concepts used in [RDFa]() can be seen in [JSON-LD]() &ndash; Compact IRIs, language- and datatyped-values, globally identified properties, and the basic graph data model of [RDF]().

Furthermore, a [JSON-LD]()-based service allows resource descriptions, that may be spread across multiple HTML pages, to be consolidated into individual _subject definitions_. By storing these _subject definitions_ in a JSON-friendly datastore such as [MongoDB](), the full power of a scaleable document store becomes available to the data otherwise spread out across numerous Wiki pages. But, the fact that the [JSON-LD]() can be fully generated from the [RDFa]() contained in the generated Wiki pages, ensures that the data will remain synchronized.

In the future, with the growth and adoption of systems such as [WikiData](), we can expect to see much of the factual information currently expressed as Wiki markup moved to outside services. The needs of the Wiki communities remain paramount, as they are at the heart of the data explosion we&#8217;ve seen in the hundreds of thousands of Wikis hosted at Wikia and elsewhere, not to mention WikiPedia and related MediaWiki projects.

As the communities become more comfortable with using knowledge stores, such as [WikiData]() and Wikia&#8217;s linked data platform, we should see a further explosion in the amount of structured information available on the web in general. The real future, then, relies not only in the efforts of communities to curate their information, but in the ability to use the principles of the Semantic Web and Linked Data to infer connections based on distributed information.

I&#8217;ll be speaking more about [JSON-LD]() and [MongoDB]() at [NoSQL Now!](http://nosql2012.dataversity.net) later this week in San Jose. Slides for my talk are available on [slideshare](http://www.slideshare.net/gkellogg1/jsonld-and-mongodb).
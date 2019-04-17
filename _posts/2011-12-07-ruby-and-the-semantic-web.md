---
id: 154
title: Ruby and the Semantic Web
date: 2011-12-07T06:21:40+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=154
permalink: /2011/12/ruby-and-the-semantic-web/
categories:
  - Uncategorized
---
This evening, I gave a talk on using Ruby [RDF.rb](http://rubygems.org/gems/rdf) and assorted gems at the [Lotico San Francisco Semantic Meetup](http://www.meetup.com/The-San-Francisco-Semantic-Web-Meetup/events/36879012/?a=cr1p_grp&rv=cr1). I've uploaded slides to [Slide Share](http://www.slideshare.net/gkellogg1/ruby-semweb-20111206).

I also showed a simple demo using the GitHub API to create FOAF and DOAP records for accounts and repositories, and to do some simple navigation. The demo is running at <https://greggkellogg.net/github-lod>, and source is (of course) available on [GitHub](http://github.com/gkellogg/github-lod).

The demo is not intended to be a complete application, but it shows some basic capabilities [Ruby LinkedData][(http://rubygems.org/gems/linkeddata) for generating RDF in a variety of formats from Active Record models (which cache the GitHub API calls). The Web-pages are, of course, marked up with RDFa, and you can use content-negotiation, or append an appropriate extension to the URLs, to retrieve the data in alternative RDF formats.
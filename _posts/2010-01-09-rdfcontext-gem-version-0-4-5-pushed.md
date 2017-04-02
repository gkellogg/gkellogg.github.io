---
id: 87
title: RdfContext gem version 0.4.5 pushed
date: 2010-01-09T00:11:44+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=87
permalink: /2010/01/rdfcontext-gem-version-0-4-5-pushed/
categories:
  - Ruby
  - Semantic Web
---
Bug fixes and minor API changes:

  * Order includes to remove platform dependencies on load requirements.
  * Fix XML Comparison matcher
  * Add `--store` option to bin/rdf_context. Let `Parser#detect_format` intuit file type.
  * Add `Graph#contents` as alias to store
  * Add `ConjunctiveGraph#triples` to return all triples from store, not just default context
  * Add `Graph#nsbinding` to retreive `Store#nsbinding`
  * Fix numerous `SQLite3Store` and `AbstractSQLStore` bugs. Add round-trip tests to Graph spec.
---
id: 293
title: CSV on the Web
date: 2015-04-16T00:00:22+00:00
author: gregg
layout: post
guid: https://greggkellogg.net/?p=293
permalink: /2015/04/csv-on-the-web/
categories:
  - Ruby
  - Semantic Web
tags:
  - CSV
  - RDF
  - Ruby
  - Tabular Data
  - W3C
---
As many who follow me know, I've long been involved in the Linked Data and Semantic Web movements. It's been my privilege to work on several successful projects, notably [RDFa](http://www.w3.org/TR/rdfa-core "HTML+RDFa 1.1"), [Microdata RDF](http://www.w3.org/TR/microdata-rdf "Microdata to RDF"), and [JSON-LD](http://www.w3.org/TR/json-ld "JSON-LD 1.0"). For the past several months I've actively been working with the W3C [CSV on the Web](http://www.w3.org/2013/csvw/wiki/Main_Page) Working Group to help define a mechanism for transforming tabular data into RDF and JSON.

Much of the data published on the Web is not HTML, or even some other structured content. In fact, a substantial amount of information is published as tabular data (comma-separated-values or tab-separated-values, commonly _CSVs_). This includes a vast amount of open government publications, such as records, weather and climate information, infrastructure assets, and so forth (see [Use Cases](http://www.w3.org/TR/csvw-ucr/) for more detail). The charter of the [CSV on the Web](http://www.w3.org/2013/csvw/wiki/Main_Page) Working Group is to provide technologies to promote greater interoperability of these datasets.

Real data is often messy, and information contained in relational databases and large spreadsheets is often lost when It's published as _CSVs_:

  * Foreign Key relationships between tables
  * Primary Keys of tables (including composite primary keys)
  * Well Known Identifiers of entities used in tables
  * Datatypes of column data, such as date/time or numeric values

The aim of the working group is to create a common [Model for Tabular Data](http://www.w3.org/TR/tabular-data-model/ "Model for Tabular Data and Metadata on the Web"), and [Metadata Format](http://www.w3.org/TR/tabular-metadata/ "Metadata Vocabulary for Tabular Data") for describing tabular data. Given this, validators, converters and viewers are enabled to work with datasets by using a common data model.

## CSV-LD

My work started about a year ago when the Working Group was being established. The group was considering using [JSON-LD](http://www.w3.org/TR/json-ld "JSON-LD 1.0") as a way of describing metadata about tables, and the work we had done on [JSON-LD Framing](http://json-ld.org/spec/latest/json-ld-framing/) seemed like a good fit for both a metadata and transformation specification. My thoughts are expressed in a wiki page on [CSV-LD](https://www.w3.org/2013/csvw/wiki/CSV-LD); the idea was that each row of a table could map to values within a JSON-LD template which could then be turned into a JSON-LD document made from each row from the spreadsheet

Looking back there are a number of over-simiplifications on this spec, but the use of JSON-LD to describe information about tabular data remains.

## CSV Data Model

The [Model for Tabular Data](http://www.w3.org/TR/tabular-data-model/ "Model for Tabular Data and Metadata on the Web") describes an abstract data model for _groups of tables_, _tables_, _columns_, _rows_ and individual _cells_. As such, it becomes a useful mechanism to refer to information when performing validation or conversion of _CSVs_. In practice, a complete in-memory data model doesn't typically need to be created, but it serves a useful purpose: Each entity in this model describes its relationship with other entities using _core annotations_; for example, the set of _tables_ in a _group of tables_, the _rows_ and _columns_ in a _table_, and the _cells_ within both _rows_ and _columns_. The model also describes other useful information, such as the _datatypes_ of a _cell value_, and if the _cell_ contains multiple values (using an internal _separator_).

A _converter_, such as a [CSV2RDF](http://w3c.github.io/csvw/csv2rdf/ "Generating RDF from Tabular Data on the Web") or [CSV2JSON](http://w3c.github.io/csvw/csv2json/ "Generating JSON from Tabular Data on the Web") converter, can then logically iterate over these entities to generate the resulting information. A _validator_ can use information about datatypes to validate cell values and foreign key relationships to verify the integrity of published information. A _viewer_ can use information about character sets, table direction and text direction to properly present information originally published as _CSVs_.

Creating a data model from _CSVs_ is the process of either starting with metadata describing those _CSVs_, or locating metadata given a _CSV_. The [Model for Tabular Data](http://www.w3.org/TR/tabular-data-model/ "Model for Tabular Data and Metadata on the Web") describes the process of locating such metadata and reconciling multiple metadata files that may be encountered. The [Metadata Format](http://www.w3.org/TR/tabular-metadata/ "Metadata Vocabulary for Tabular Data") then describes the structure of this data (syntactically, as a [JSON-LD](http://www.w3.org/TR/json-ld "JSON-LD 1.0") formatted document) and working with it to create the basis of the _core annotations_ of the _annotated data model_.

As an example, I recently worked with others to release the [rdf-vocab](http://rubygems.org/gems/rdf-vocab) Ruby gem, which includes a Ruby version of different RDF Ontologies/Vocabularies used for reasoning on things such as the [Structured Data Linter](http://linter.structured-data.org/). One vocabulary we very much wanted to include as the [IANA Link Relations](http://www.iana.org/assignments/link-relations/link-relations.xhtml). This is not published as any kind of RDF dataset, much less as an RDFS or OWL vocabulary definition. In fact, this has prevented it from being used in different standards work, as the link relation for `describes` cannot easily be dereferenced to find It's definition. Formally, the `describes` link relation maps to the `http://www.iana.org/assignments/link-relations/describes` URI, but dereferencing this results in an HTTP 404 status. However, this doesn't need to get in the way of defining an RDFS vocabulary based on this dataset. Thankfully, the link relations are published in other formats, including [csv](http://www.iana.org/assignments/link-relations/link-relations-1.csv). This allows us to construct a [metadata description](https://github.com/ruby-rdf/rdf-vocab/blob/develop/etc/iana-metadata.json) of this dataset:

    {
      "@context": "http://www.w3.org/ns/csvw",
      "url": "http://www.iana.org/assignments/link-relations/link-relations-1.csv",
      "tableSchema": {
        "aboutUrl": "{name}",
        "lang": "en",
        "columns": [
          {"name": "name", "title": "Relation Name", "propertyUrl": "rdfs:label"},
          {"name": "comment", "title": "Description", "propertyUrl": "rdfs:comment"},
          {"name": "type", "title": "Reference", "propertyUrl": "rdf:type", "valueUrl": "rdf:Property"},
          {"title": "Notes", "suppressOutput": true}
        ]
      }
    }
    

This says that there is a table located at `http://www.iana.org/assignments/link-relations/link-relations-1.csv` with a _schema_. Each row identifies a different _subject_ using the `aboutUrl` property. The `"{name}"` value is a [URI template](https://tools.ietf.org/html/rfc6570) that creates a URI from the `name` variable relative to the location of the _CSV_.

Each column is given a `name` and `title`, along with a `propertyUrl` which is used to relate the _subject_ with the value of a given cell. The first two columns are textual, and result in an `RDF Literal` when mapped to RDF. The `lang` property defines the overall language to apply to plain literals in this dataset. The third column is turned into a URI using the `valueUrl` property and the fourth column is simply ignored; note that the cell contents of the third column aren't actually used to create the value, and the value is hard-coded as `rdf:Property`.

Mapping this on the CSV, for example, turns the first row into the following RDF:

    <http://www.iana.org/assignments/link-relations/about> rdfs:label "about@en";
      rdfs:comment "Refers to a resource that is the subject of the link's context."@en;
      rdf:type rdf:Property .
    

Within the [RDF.rb](http://rdf.rubyforge.org/) tool chain, this allows the use of `RDF::Vocab::IANA.about` to refer to this property definition. The reasoner knows It's a property, because It's type if `rdf:Property`. The completely transformed CSV can be found [here](https://raw.githubusercontent.com/ruby-rdf/rdf-vocab/develop/etc/iana.ttl).

The group has just released new working drafts for [Model for Tabular Data](http://www.w3.org/TR/tabular-data-model/ "Model for Tabular Data and Metadata on the Web") and the [Metadata Format](http://www.w3.org/TR/tabular-metadata/ "Metadata Vocabulary for Tabular Data"), along with [CSV2RDF](http://w3c.github.io/csvw/csv2rdf/ "Generating RDF from Tabular Data on the Web") and [CSV2JSON](http://w3c.github.io/csvw/csv2json/ "Generating JSON from Tabular Data on the Web").

## An implementation in Ruby

My method for working on specifications is to develop code implementing these specifications as they are being developed. This, of necessity, means that there is a lot of work (and re-work) as the details are being worked out, but I view it as a necessary part of spec development to ensure that the result can be implemented. In this case, this is embodied in the [rdf-tabular](http://rubygems.org/gems/rdf-tabular) gem, which implements the [Model for Tabular Data](http://www.w3.org/TR/tabular-data-model/ "Model for Tabular Data and Metadata on the Web") and [Metadata Format](http://www.w3.org/TR/tabular-metadata/ "Metadata Vocabulary for Tabular Data") as well as both [CSV2RDF](http://w3c.github.io/csvw/csv2rdf/ "Generating RDF from Tabular Data on the Web") and [CSV2JSON](http://w3c.github.io/csvw/csv2json/ "Generating JSON from Tabular Data on the Web") and much of the validation required for these to work. As is true for all [RDF.rb](http://rdf.rubyforge.org/) gems, this is available in the public domain ([Unlicense](http://unlicense.org)) and is freely usable by anyone wishing to get a start on their own implementation.

You can try this out on my [RDF Distiller](http://rdf.greggkellogg.net/distiller), or using the [Structured Data Linter](http://linter.structured-data.org/).

## Test Suite

With updated working drafts, the next phase of the CSVW Working Group will be to develop more tests. There is already a fair [Test Suite](http://w3c.github.io/csvw/tests/) available which probes various corner-cases of validating and converting tabular data. There are already over 80 tests defined, and more will be forthcoming in the coming weeks and months. (Disclaimer, these tests have yet to be accepted by the WG).

A forthcoming blog entry will delve into the basis of a simple implementation, and what it takes to completely conform to the specification.
---
id: 303
title: Implementing CSV on the Web
date: 2015-04-18T20:14:54+00:00
author: gregg
layout: post
guid: http://greggkellogg.net/?p=303
permalink: /2015/04/implementing-csv-on-the-web/
dsq_thread_id:
  - "5689823482"
dsq_needs_sync:
  - "1"
categories:
  - Ruby
  - Semantic Web
---
As I blogged about [before](http://greggkellogg.net/2015/04/csv-on-the-web/), I've implemented the [current drafts](http://www.w3.org/blog/data/2015/04/16/csv-on-the-web-seeking-comments-and-implementations/) of [CSV on the Web](http://www.w3.org/2013/csvw/wiki/Main_Page) in the [rdf-tabular gem](http://rubygems.org/gems/rdf-tabular). The gem is is available from [rdf-tabular repo](http://github.com/ruby-rdf/rdf-tabular) and is in the public domain ([Unlicense](http://unlicense.org)) and is freely usable by anyone wishing to get a start on their own implementation. For those wishing to take an incremental approach, this post describes the basic workings of the gem, highlights more advanced cases necessary to pass the [Test Suite](http://w3c.github.io/csvw/tests/) and attempts to provide some insight into the process of implementing the specifications.

## CSVW – in a nutshell

The basic purpose of the CSVW specifications is to do an informed process of CSVs into an _annotated data model_, and use this model as the basis of creating RDF or JSON. At a minimum, this means assuming that the first row of a CSV is a _header row_ containing _titles_ for cells in that CSV, and that each row constitutes a record with properties based on the _column titles_ with values from each cell. We'll use the canonical example of the [tree-ops](http://w3c.github.io/csvw/tests/tree-ops.csv) example from the [Model for Tabular Data](http://www.w3.org/TR/tabular-data-model/ "Model for Tabular Data and Metadata on the Web")

    GID,On Street,Species,Trim Cycle,Inventory Date
    1,ADDISON AV,Celtis australis,Large Tree Routine Prune,10/18/2010
    2,EMERSON ST,Liquidambar styraciflua,Large Tree Routine Prune,6/2/2010
    

This example has a header row and two data rows. The simple transformation of this to RDF without any external metadata would yield the following (in minimal mode):

    @base <http://w3c.github.io/csvw/tests/tree-ops.csv> .
    
    [
      <#GID> "2";
      <#Inventory%20Date> "6/2/2010";
      <#On%20Street> "EMERSON ST";
      <#Species> "Liquidambar styraciflua";
      <#Trim%20Cycle> "Large Tree Routine Prune"
    ] .
    
    [
      <#GID> "1";
      <#Inventory%20Date> "10/18/2010";
      <#On%20Street> "ADDISON AV";
      <#Species> "Celtis australis";
      <#Trim%20Cycle> "Large Tree Routine Prune"
    ] .
    

This is minimally useful, but we can use this as a walk-through of how the [rdf-tabular gem](http://rubygems.org/gems/rdf-tabular) creates this.

The first step is to retrieve the document from It's URL: `http://w3c.github.io/csvw/tests/tree-ops.csv`. We then can look for metadata associated with the file to inform the creation of the _annotated table_; this information might come from by the user specifying to location of metadata separately, from an HTTP Link header, by looking at `http://w3c.github.io/csvw/tests/tree-ops.csv-metadata.json` for file-specific metadata, or at `http://w3c.github.io/csvw/tests/metadata.json` for directory-specific metadata. In this case, there is none, so we constructed _embedded metatata_ from just the _header row_. The process for locating metadata is described in [Creating Annotated Tables](http://www.w3.org/TR/tabular-data-model/#creating-annotated-tables), and creating embedded metadata is described in [Parsing Tabular Data](http://www.w3.org/TR/tabular-data-model/#parsing). Basically, we're looking to create a _table description_ with a _schema_ describing each _column_ resulting in the following:

    {
      "@context": "http://www.w3.org/ns/csvw",
      "url": "http://w3c.github.io/csvw/tests/tree-ops.csv",
      "tableSchema": {
        "columns": [
          {"titles": "GID"},
          {"titles": "On Street"},
          {"titles": "Species"},
          {"titles": "Trim Cycle"},
          {"titles": "Inventory Date"}
        ]
      }
    }
    

By following the rules in the [Metadata Format](http://www.w3.org/TR/tabular-metadata/ "Metadata Vocabulary for Tabular Data"), we then create annotations on an _annotated table_ (an abstract model, shown in arbitrary JSON):

    {
      "@type": "AnnotatedTableGroup",
      "tables": [
        {
          "@type": "AnnotatedTable",
          "url": "http://w3c.github.io/csvw/tests/tree-ops.csv",
          "columns": [
            {
              "@id": "http://w3c.github.io/csvw/tests/tree-ops.csv#col=1",
              "@type": "Column",
              "number": 1,
              "sourceNumber": 1,
              "cells": [],
              "name": "GID",
              "titles": {"und": ["GID"]}
            },
            {
              "@id": "http://w3c.github.io/csvw/tests/tree-ops.csv#col=2",
              "@type": "Column",
              "number": 2,
              "sourceNumber": 2,
              "cells": [],
              "name": "On%20Street",
              "titles": {"und": ["On Street"]}
            },
            {
              "@id": "http://w3c.github.io/csvw/tests/tree-ops.csv#col=3",
              "@type": "Column",
              "number": 3,
              "sourceNumber": 3,
              "cells": [],
              "name": "Species",
              "titles": {"und": ["Species"]}
            },
            {
              "@id": "http://w3c.github.io/csvw/tests/tree-ops.csv#col=4",
              "@type": "Column",
              "number": 4,
              "sourceNumber": 4,
              "cells": [],
              "name": "Trim%20Cycle",
              "titles": {"und": ["Trim Cycle"]}
            },
            {
              "@id": "http://w3c.github.io/csvw/tests/tree-ops.csv#col=5",
              "@type": "Column",
              "number": 5,
              "sourceNumber": 5,
              "cells": [],
              "name": "Inventory%20Date",
              "titles": {"und": ["Inventory Date"]}
            }
          ],
          "rows": []
        }
      ]
    }
    

Here we gather some metadata based on the row and column numbers (logical and actual, which are the same here) and create a default for the _column name_ from _titles_. Also, note that _titles_ is expanded to a [natural language property](http://w3c.github.io/csvw/metadata/#dfn-natural-language-property), which is basically a [JSON-LD Language Map](http://www.w3.org/TR/json-ld/#language-maps) where `und` is used when there is no language. The _column name_ is percent encoded so that it can be used in a [URI template](https://tools.ietf.org/html/rfc6570).

Note that the `rows` and `cells` values are empty, as we haven't actually processed any rows from the input yet.

The [rdf-tabular gem](http://rubygems.org/gems/rdf-tabular) implements the [RDF::Reader](http://www.rubydoc.info/github/ruby-rdf/rdf/RDF/Reader) pattern, which takes care of much of the process of opening the file and getting useful metadata. In the case of the [rdf-tabular gem](http://rubygems.org/gems/rdf-tabular), this includes the steps described in [Creating Annotated Tables](http://www.w3.org/TR/tabular-data-model/#creating-annotated-tables) which involves recursive invocations of the reader, but nominally, it yields a _reader instance_ which implements the [RDF::Enumerable](http://www.rubydoc.info/github/ruby-rdf/rdf/RDF/Enumerable) pattern. In particular, the reader implements an `#each` method to yield each RDF Statement (triple); this is where the work actually happens. A call looks like the following:

    RDF::Tabular::Reader.open("http://w3c.github.io/csvw/tests/tree-ops.csv") do |reader|
      reader.each do |statement|
        puts statement.inspect
      end
    end
    

Basically, the job of the reader is to create the _abstract tabular data model_ and use it to read each row of the table(s) described in the model and use that to generate RDF triples (it can also be used to generate JSON output without going the RDF, but that's another story).

The [rdf-tabular gem](http://rubygems.org/gems/rdf-tabular) implements `RDF::Tabular::Metadata`, with subclasses for the different kinds of metadata we need. This provides the `#each_row` method, which given an input file yields each row. For the Ruby implementation, we use the [CSV library](http://docs.ruby-lang.org/en/2.1.0/CSV.html) and use dialect information to set parsing defaults. The gist of the implementation looks like the following:

    def each_row(input)
      csv = ::CSV.new(input, csv_options)
      # Skip skipRows and headerRowCount
      number, skipped = 0, (dialect.skipRows.to_i + dialect.headerRowCount)
      (1..skipped).each {csv.shift}
      csv.each do |data|
        number += 1
        yield(Row.new(data, self, number, number + skipped))
      end
    end
    

A `Row` then abstracts information for each table row and provides the cells for that row:

    # Wraps each resulting row
    class Row
      attr_reader :values
    
      # Class for returning values
      Cell = Struct.new(:table, :column, :row, :stringValue, :value, :errors)
    
      def initialize(row, metadata, number, source_number)
        @values = []
        skipColumns = metadata.dialect.skipColumns.to_i
        columns = metadata.tableSchema.columns
        row.each_with_index do |value, index|
          next if index < skipColumns
          column = columns[index - skipColumns]
          @values << cell = Cell.new(metadata, column, self, value)
        end
      end
    end
    

There's more to this in the actual implementation, of course, but this handles a simple value.

Now we can implement `RDF::Tabular::Reader#each_statement`:

    def each_statement(&block)
      metadata.each_row(input) do |row|
        default_cell_subject = RDF::Node.new
        row.values.each_with_index do |cell, index|
          propertyUrl = RDF::URI("#{metadata.url}##{cell.column.name}")
          yield RDF::Statement(default_cell_subject, propertyUrl, cell.value)
        end
      end
    end
    

That's pretty much the basis of a Ruby implementation. There's more work to do in `#each_statement`, as It's initially invoked with a _TableGroup_, which recursively invokes it again for each _Table_, which then calls again for the actual CSV, but that's all setup. There's also work in `RDF::Tabular::Reader#initialize` to find the metadata, ensure that it is compatible with the actual tables, and so forth.

## Fleshing out with more details

So, this implements a basic reader interface from a CSV using the _abstract tabular data model_, but the output's not too interesting. What if we want to make the data richer:

  * Give unique identifiers (_subjects_) to the cells in a row
  * Use _subjects_ for different cells
  * Define _property URIs_ for each cell
  * Assign and match cell _string values_ to datatypes
  * Parse microformats within a given cell

For that we need to define a Metadata file.

### Defining Metadata

A Metadata file is a [JSON-LD](http://www.w3.org/TR/json-ld "JSON-LD 1.0") document (really, it has the structure of a JSON-LD document, it is parsed as JSON) which allows us to define _properties_ on metadata _declarations_ which directly relate to the _abstract tabular data model_. For example, let's look at the metadata description for the [tree-ops](http://w3c.github.io/csvw/tests/test011/tree-ops.csv) example: [tree-ops.csv-metadata.json](http://w3c.github.io/csvw/tests/test011/tree-ops.csv-metadata.json):

    {
      "@context": ["http://www.w3.org/ns/csvw", {"@language": "en"}],
      "url": "tree-ops.csv",
      "dc:title": "Tree Operations",
      "dcat:keyword": ["tree", "street", "maintenance"],
      "dc:publisher": {
        "schema:name": "Example Municipality",
        "schema:url": {"@id": "http://example.org"}
      },
      "dc:license": {"@id": "http://opendefinition.org/licenses/cc-by/"},
      "dc:modified": {"@value": "2010-12-31", "@type": "xsd:date"},
      "tableSchema": {
        "columns": [{
          "name": "GID",
          "titles": ["GID", "Generic Identifier"],
          "dc:description": "An identifier for the operation on a tree.",
          "datatype": "string",
          "required": true
        }, {
          "name": "on_street",
          "titles": "On Street",
          "dc:description": "The street that the tree is on.",
          "datatype": "string"
        }, {
          "name": "species",
          "titles": "Species",
          "dc:description": "The species of the tree.",
          "datatype": "string"
        }, {
          "name": "trim_cycle",
          "titles": "Trim Cycle",
          "dc:description": "The operation performed on the tree.",
          "datatype": "string"
        }, {
          "name": "inventory_date",
          "titles": "Inventory Date",
          "dc:description": "The date of the operation that was performed.",
          "datatype": {"base": "date", "format": "M/d/yyyy"}
        }],
        "primaryKey": "GID",
        "aboutUrl": "#gid-{GID}"
      }
    }
    

Here we have a couple of different things going on. Note the following:

    {
      "@context": ["http://www.w3.org/ns/csvw", {"@language": "en"}],
      "url": "tree-ops.csv",
      "dc:title": "Tree Operations",
      "dcat:keyword": ["tree", "street", "maintenance"],
      "dc:publisher": {
        "schema:name": "Example Municipality",
        "schema:url": {"@id": "http://example.org"}
      },
      "dc:license": {"@id": "http://opendefinition.org/licenses/cc-by/"},
      "dc:modified": {"@value": "2010-12-31", "@type": "xsd:date"},
      ...
    }
    

The _prefixed name_ properties are _common properties_, basically just JSON-LD that's inserted into the model to define _annotations_ on the model. In this case, their defined on a _Table_, so they annotate that model. As it is JSON-LD, the string values take the `@langauge` defined in the context. CSVW uses a [dialect of JSON-LD](http://www.w3.org/TR/tabular-metadata/#json-ld-dialect) which places some restrictions on what can go here. Basically, nothing more can go into the `@context` besides `@language` and `@base`; it also must use `http://www.w3.org/ns/csvw` and only the terms and prefixes defined within that context can be used along with absolute IRIs.

<pre>{
  "@context": ["http://www.w3.org/ns/csvw", {"@language": "en"}],
  "url": "tree-ops.csv",
  ...
  "tableSchema": {
    "columns": [{
      "name": "GID",
      "titles": ["GID", "Generic Identifier"],
      "dc:description": "An identifier for the operation on a tree.",
      "datatype": "string",
      "required": true
    }, {
      "name": "on_street",
      "titles": "On Street",
      "dc:description": "The street that the tree is on.",
      "datatype": "string"
    }, {
      "name": "species",
      "titles": "Species",
      "dc:description": "The species of the tree.",
      "datatype": "string"
    }, {
      "name": "trim_cycle",
      "titles": "Trim Cycle",
      "dc:description": "The operation performed on the tree.",
      "datatype": "string"
    }, {
      "name": "inventory_date",
      "titles": "Inventory Date",
      "dc:description": "The date of the operation that was performed.",
      "datatype": {"base": "date", "format": "M/d/yyyy"}
    }],
    "primaryKey": "GID",
    "aboutUrl": "#gid-{GID}"
  }
}
</pre>

The `tableSchema` property tells us that this is a _Table description_; we could also have added a `"@type": "Table"` property to make this explicit, but It's not necessary. Metadata always starts with either a _Table description_ or a _TableGroup description_.

We define an explicit _name_ property for the first column. If we didn't, it would take the first value from _titles_. The _column_ has a _common property_, which is presently not used in the transformation, but exists in the _annotated tabular data model_. It also declares the data type to be `string`, which is a synonym for `xsd:string`, and a value is `required`, meaning that it is considered an error if a cell value has an empty string (or one matching that defined using the `null` annotation). Note that `datatype` is an _inherited property_, meaning that it could have been defined on the _Table_, _Schema_ or _Column_ and would be in scope for all cells based on the inheritance model.

The last column uses a complex datatype: It is based on `xsd:date` and uses a `format` string to match string values and map them onto the datatype. This allows a date of the form 4/17/2015 to be interpreted as `"2015-04-17"^^xsd:date` by using _date field symbols_ as defined in [UAX35](http://www.unicode.org/reports/tr35/tr35-31/tr35.html).

All of these map pretty much as you would expect onto the _annotated tabular data model_:

    {
      "@type": "AnnotatedTableGroup",
      "tables": [
        {
          "@type": "AnnotatedTable",
          "url": "http://w3c.github.io/csvw/tests/test011/tree-ops.csv",
          "dc:title": {"@value": "Tree Operations","@language": "en"},
          "dcat:keyword": [
            {"@value": "tree","@language": "en"},
            {"@value": "street","@language": "en"},
            {"@value": "maintenance","@language": "en"}
          ],
          "dc:publisher": {
            "schema:name": {"@value": "Example Municipality","@language": "en"},
            "schema:url": {"@id": "http://example.org"}
          },
          "dc:license": {"@id": "http://opendefinition.org/licenses/cc-by/"},
          "dc:modified": {"@value": "2010-12-31","@type": "xsd:date"},
          "columns": [
            {
              "@id": "http://w3c.github.io/csvw/tests/test011/tree-ops.csv#col=1",
              "@type": "Column",
              "number": 1,
              "sourceNumber": 1,
              "cells": [],
              "name": "GID",
              "titles": {"en": ["GID", "Generic Identifier"]},
              "dc:description": {"@value": "An identifier for the operation on a tree.", "@language": "en"},
              "datatype": {"base": "string"},
              "required": true
            },
            {
              "@id": "http://w3c.github.io/csvw/tests/test011/tree-ops.csv#col=2",
              "@type": "Column",
              "number": 2,
              "sourceNumber": 2,
              "cells": [],
              "name": "on_street",
              "titles": {"en": ["On Street"]},
              "dc:description": {"@value": "The street that the tree is on.","@language": "en"},
              "datatype": {"base": "string"}
            },
            {
              "@id": "http://w3c.github.io/csvw/tests/test011/tree-ops.csv#col=3",
              "@type": "Column",
              "number": 3,
              "sourceNumber": 3,
              "cells": [],
              "name": "species",
              "titles": {"en": ["Species"]},
              "dc:description": {"@value": "The species of the tree.","@language": "en"},
              "datatype": {"base": "string"}
            },
            {
              "@id": "http://w3c.github.io/csvw/tests/test011/tree-ops.csv#col=4",
              "@type": "Column",
              "number": 4,
              "sourceNumber": 4,
              "cells": [],
              "name": "trim_cycle",
              "titles": {"en": ["Trim Cycle"]},
              "dc:description": {"@value": "The operation performed on the tree.","@language": "en"},
              "datatype": {"base": "string"}
            },
            {
              "@id": "http://w3c.github.io/csvw/tests/test011/tree-ops.csv#col=5",
              "@type": "Column",
              "number": 5,
              "sourceNumber": 5,
              "cells": [],
              "name": "inventory_date",
              "titles": {"en": ["Inventory Date"]},
              "dc:description": {"@value": "The date of the operation that was performed.","@language": "en"},
              "datatype": {"base": "date", "format": "M/d/yyyy"}
            }
          ],
          "rows": []
        }
      ]
    }
    

Note that the _common properties_ have been expanded and the `@context` simplified to remove `@language`. Also `datatype` values have been normalized to the expanded form. This happens through the process of _Normalization_.

### Normalization

[Normalization](http://www.w3.org/TR/tabular-metadata/#normalization) places metadata into a consistent format where compact values are expanded, values which may be an _Array_ are made into an _Array_, _link properties_ have their URL expanded based on the base of the metadata and _object properties_ which are in the form of a URL string are opened and replaced with the content of the resource they reference.

### Ensuring Metadata Compatibility (was Merging Metadata)

The [Locating Metadata](http://www.w3.org/TR/tabular-data-model/#locating-metadata) describes the various places metadata files may be found. [Creating Annotated Tables](http://www.w3.org/TR/tabular-data-model/#creating-annotated-tables) defines the process of creating the final metadata when starting with either a Metadata File or a CSV. This calls for ensuring [Metadata Compatibility](http://w3c.github.io/csvw/syntax/#metadata-compatibility). Given metadata, this can be used to find one or more tabular data files. These files are considered _compatible_ with the metadata, if they are referenced from the metadata (i.e., there is a Table Description referencing that particular tabular data file), and the columns in the Table Description Schema, match the columns in the tabular data file by comparing titles or names from the column metadata with the title of the tabular data file column, if it has one. This is necessary to ensure that the CSV file matches the schema described in metadata.

### URI Templates

In the [tree-ops-virtual.json](http://w3c.github.io/csvw/examples/tree-ops-virtual.json) example, _URI template properties_ were defined:

    {
      "url": "tree-ops.csv",
      "@context": ["http://www.w3.org/ns/csvw", {"@language": "en"}],
      "tableSchema": {
        "columns": [{
          "name": "GID",
          "titles": "GID",
          "datatype": "string",
          "propertyUrl": "schema:url",
          "valueUrl": "#gid-{GID}"
        }, {
          "name": "on_street",
          "titles": "On Street",
          "datatype": "string",
          "aboutUrl": "#location-{GID}",
          "propertyUrl": "schema:streetAddress"
        }, {
          "name": "species",
          "titles": "Species",
          "datatype": "string",
          "propertyUrl": "schema:name"
        }, {
          "name": "trim_cycle",
          "titles": "Trim Cycle",
          "datatype": "string"
        }, {
          "name": "inventory_date",
          "titles": "Inventory Date",
          "datatype": {"base": "date", "format": "M/d/yyyy"},
          "aboutUrl": "#event-{inventory_date}",
          "propertyUrl": "schema:startDate"
        }, {
          "propertyUrl": "schema:event",
          "valueUrl": "#event-{inventory_date}",
          "virtual": true
        }, {
          "propertyUrl": "schema:location",
          "valueUrl": "#location-{GID}",
          "virtual": true
        }, {
          "aboutUrl": "#location-{GID}",
          "propertyUrl": "rdf:type",
          "valueUrl": "schema:PostalAddress",
          "virtual": true
        }],
        "aboutUrl": "#gid-{GID}"
      }
    }
    

This introduces several new concepts:

  * The _Schema_ has an `aboutUrl` property: **"#gid-{GID}"**. This is a [URI Template](https://tools.ietf.org/html/rfc6570), where _GID_ acts as a variable, taking the _cell value_ of the cell in the _GID_ column to construct a URI. In this case it constructs values such as `<http://w3c.github.io/csvw/examples/tree-ops.csv#gid-1>`. This is because the _Table_ `url` is `tree-ops.csv`, which is a URL relative to the location of the metadata file. In the first row, the value of the `GID` column is `1`, so that is substituted to create **"#gid-1"**, then resolved against `url`. This `aboutUrl` then defines the default subject for all cells within that row.

The first column has **"propertyUrl": "schema:url"**, which turns into the absolute URL `http://schema.org/url`, and is used as the _predicate_ for that cell. As the column as a `valueUrl` (**"valueUrl": "#gid-{GID}"**), that is expanded and used as the object for that cell. Thus, the first cell of the first row would result in the following triple:

    <#gid-1> schema:url <#gid-1> .
    

(relative to the URL file location).

The second column has It's own `aboutUrl` (**"aboutUrl": "#location-{GID}"**), meaning that the _subject_ for triples for this column are different than the _default subject_.

The last three columns are _virtual columns_, as they don't correspond to data actually in the CSV; these are used for injecting information into the row. When fully processed, the following RDF is created (again, in minimal mode):

    @base <http://w3c.github.io/csvw/examples/tree-ops.csv> .
    @prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
    @prefix schema: <http://schema.org/> .
    @prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
    
    <#event-2010-06-02> schema:startDate "2010-06-02"^^xsd:date .
    
    <#event-2010-10-18> schema:startDate "2010-10-18"^^xsd:date .
    
    <#gid-1> schema:event <#event-2010-10-18>;
       schema:location <#location-1>;
       schema:name "Celtis australis";
       schema:url <#gid-1>;
       <#trim_cycle> "Large Tree Routine Prune" .
    
    <#gid-2> schema:event <#event-2010-06-02>;
       schema:location <#location-2>;
       schema:name "Liquidambar styraciflua";
       schema:url <#gid-2>;
       <#trim_cycle> "Large Tree Routine Prune" .
    
    <#location-1> a schema:PostalAddress;
       schema:streetAddress "ADDISON AV" .
    
    <#location-2> a schema:PostalAddress;
       schema:streetAddress "EMERSON ST" .
    

Processing this requires some enhancement to `RDF::Tabular::Row#initialize` method:

    # Wraps each resulting row
    class Row
      attr_reader :values
    
      # Class for returning values
      Cell = Struct.new(:table, :column, :row, :stringValue, :aboutUrl, :propertyUrl, :valueUrl, :value, :errors) do
    
        def set_urls(mapped_values)
          %w(aboutUrl propertyUrl valueUrl).each do |prop|
            # If the cell value is nil, and it is not a virtual column
            next if prop == "valueUrl" && value.nil? && !column.virtual
            if v = column.send(prop.to_sym)
              t = Addressable::Template.new(v)
              mapped = t.expand(mapped_values).to_s
              url = row.context.expand_iri(mapped, documentRelative: true)
              self.send("#{prop}=".to_sym, url)
            end
          end
        end
    
      end
    
      def initialize(row, metadata, number, source_number)
        @values = []
        skipColumns = metadata.dialect.skipColumns.to_i
        columns = metadata.tableSchema.columns
        row.each_with_index do |value, index|
          next if index < skipColumns
          column = columns[index - skipColumns]
          @values << cell = Cell.new(metadata, column, self, value)
    
          datatype = column.datatype || Datatype.new(base: "string", parent: column)
          cell_value = value_matching_datatype(value.dup, datatype, expanded_dt, column.lang)
          map_values[columns[index - skipColumns].name] =  cell_value.to_s
    
        end
    
        # Map URLs for row
        ...
      end
    end
    

This introduces datatype matching through `#value_matching_datatype` (not detailed), creates a `map_values` structure that can be used for [URI template](https://tools.ietf.org/html/rfc6570) processing, and then calls `Cell#set_urls` to actually create `aboutUrl`, `propertyUrl`, and `valueUrl` annotations on the cell.

The `#each_statement` method is updated to take these into consideration:

    def each_statement(&block)
      metadata.each_row(input) do |row|
        default_cell_subject = RDF::Node.new
        row.values.each_with_index do |cell, index|
          cell_subject = cell.aboutUrl || default_cell_subject
          propertyUrl = cell.propertyUrl || RDF::URI("#{metadata.url}##{cell.column.name}")
            yield RDF::Statement(cell_subject, propertyUrl, cell.column.valueUrl || cell.value)
        end
      end
    end
    

Here the only difference is that we use `cell.aboutUrl`, `cell.propertyUrl`, and `cell.valueUrl` if they are defined, and the defaults otherwise.

### Multiple Values

Microsyntaxes are common in CSVs, and there are many different kind. A microsyntax is some convention for formatting information within a cell. CSVW supports delimited values within a cell, so that a list of elements can be provided, allowing a single cell to contain multiple values.

For example the [tree-ops-ext.csv](http://w3c.github.io/csvw/examples/tree-ops-ext.csv) example allows for multiple comments on a record using ";" as a separator:

    GID,On Street,Species,Trim Cycle,Diameter at Breast Ht,Inventory Date,Comments,Protected,KML
    1,ADDISON AV,Celtis australis,Large Tree Routine Prune,11,10/18/2010,,,"<Point><coordinates>-122.156485,37.440963</coordinates></Point>"
    2,EMERSON ST,Liquidambar styraciflua,Large Tree Routine Prune,11,6/2/2010,,,"<Point><coordinates>-122.156749,37.440958</coordinates></Point>"
    6,ADDISON AV,Robinia pseudoacacia,Large Tree Routine Prune,29,6/1/2010,cavity or decay; trunk decay; codominant leaders; included bark; large leader or limb decay; previous failure root damage; root decay;  beware of BEES,YES,"<Point><coordinates>-122.156299,37.441151</coordinates></Point>"
    

The [metadata file](http://w3c.github.io/csvw/examples/tree-ops-ext.csv-metadata.json) adds the `separator` property to the _comments_ column:

    {
      "tableSchema": {
        "columns": [
          ...
          {
            "name": "comments",
            "titles": "Comments",
            "dc:description": "Supplementary comments relating to the operation or tree.",
            "datatype": "string",
            "separator": ";"
          }
        ]
      }
    }
    

The effect of `separator` is to split the string value into multiple values and parse them using any `datatype` description. This is shown using slight modifications to `RDF::Tabular::Row#initialize` and `RDF::Tabular::Reader#each_statement`:

    # Wraps each resulting row
    class Row
      attr_reader :values
      def initialize(row, metadata, number, source_number)
        @values = []
        skipColumns = metadata.dialect.skipColumns.to_i
        columns = metadata.tableSchema.columns
        row.each_with_index do |value, index|
          next if index < skipColumns
          column = columns[index - skipColumns]
          @values << cell = Cell.new(metadata, column, self, value)
          datatype = column.datatype || Datatype.new(base: "string", parent: column)
    
    
          cell_values = column.separator ? value.split(column.separator) : [value]
    
          cell_values = cell_values.map do |v|
            value_matching_datatype(v.dup, datatype, expanded_dt, column.lang)
          end
    
          cell.value = (column.separator ? cell_values : cell_values.first)
    
          map_values[columns[index - skipColumns].name] =  (column.separator ? cell_values.map(&:to_s) : cell_values.first.to_s)
        end
    
        # Map URLs for row
        @values.each_with_index do |cell, index|
          mapped_values = map_values.merge(
            "_name" => URI.decode(cell.column.name),
            "_column" => cell.column.number,
            "_sourceColumn" => cell.column.sourceNumber
          )
          cell.set_urls(mapped_values)
        end
    
      end
    end
    

The only change to `#each_statement` is to consider multiple cell values:

    def each_statement(&block)
      metadata.each_row(input) do |row|
        default_cell_subject = RDF::Node.new
        row.values.each_with_index do |cell, index|
          cell_subject = cell.aboutUrl || default_cell_subject
          propertyUrl = cell.propertyUrl || RDF::URI("#{metadata.url}##{cell.column.name}")
    
          if cell.column.valueUrl
            yield RDF::Statement(cell_subject, propertyUrl, cell.column.valueUrl)
          else
            Array(cell.value).each do |v|
              yield RDF::Statement(cell_subject, propertyUrl, v)
            end
          end
    
        end
      end
    end
    

### Common Properties and Notes

Common Properties and Notes are simply embedded [JSON-LD](http://www.w3.org/TR/json-ld "JSON-LD 1.0") within the metadata file (although limited) to a specific [dialect](http://www.w3.org/TR/tabular-metadata/#json-ld-dialect) to simplify implementations. In Ruby, of course, we have available a full [json-ld gem](http://rubygems.org/gems/json-ld), so turning this into RDF shouldn't present any problems.

## Conclusion

This article has shown the basis for implementing the [CSV on the Web](http://www.w3.org/2013/csvw/wiki/Main_Page) specifications, and hopefully will aid in more implementations.

## Update 2014-05-14

This post has been updated to reflect the fact the metadata merging has been removed from CSVW, simplifying an implementation even further.
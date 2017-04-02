---
id: 163
title: The Use of JSON-LD in the RDFa Test Harness
date: 2012-03-20T22:52:12+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=163
permalink: /2012/03/the-use-of-json-ld-in-the-rdfa-test-harness/
categories:
  - JavavScript
  - Ruby
  - Semantic Web
---
This article is the second in a three-part series on implementing the [RDFa Test Suite](http://rdfa.info/test-suite/). The [first article](http://greggkellogg.net/2012/03/18/a-new-rdfa-test-harness) discussed the use of [Sinatra](http://www.sinatrarb.com/), [Backbone.js](http://backbonejs.org/) and [Bootstrap.js](http://twitter.github.com/bootstrap/index.html) in creating the test harness. In this article, we focus on [JSON-LD](http://json-ld.org/), a Linked Data technology that complements [RDFa](http://rdfa.info/) is creating modern Web applications.

## Test Manifest

The RDFa [test manifest](http://rdfa.info/test-suite/manifest.ttl) is a [Turtle](http://www.w3.org/TR/turtle/) document used to specify the tests that apply to different versions and host languages in [RDFa](http://rdfa.info/). [Turtle](http://www.w3.org/TR/turtle/) is a great language for representing information in a reasonably human-understandable way. Most people authoring RDF by hand stick to Turtle, because of it&#8217;s ease of use and concise way of expressing Linked Data graphs. For example, to specify a specific test entry, we could write some Turtle as follows:

    <test-cases/0001> a test:TestCase;
       dc:title "Predicate establishment with @property";
       rdfatest:rdfaVersion "rdfa1.0", "rdfa1.1";
       rdfatest:hostLanguage "xml", "xhtml1", "html4", "html5", "xhtml5";
       test:classification test:required;
       test:informationResourceInput <test-cases/0001.html>;
       test:informationResourceResults <test-cases/0001.sparql> .
    

Basically, this defines a (relative) URL identifying the test case, gives it a title, describes the relevant RDFa versions and host languages, says it&#8217;s required, and shows the files used to provide input and to test the results. The problem is, this is not a convenient form to use programatically. Modern Web applications make use of JSON for representing data, for one reason because JSON can be represented natively in JavaScript, but also because it has a convenient representation in Ruby and other languages.

Let&#8217;s look at the equivalent test representation in [JSON-LD](http://json-ld.org/):

    {
      "@context": "http://rdfa.info/contexts/rdfa-test.jsonld",
      "@graph": [
        {
          "@id": "http://rdfa.info/test-suite/test-cases/0001",
          "@type": "test:TestCase",
          "num": "0001",
          "classification": "test:required",
          "description": "Predicate establishment with @property",
          "input": "http://rdfa.info/test-suite/test-cases/0001.html",
          "results": "http://rdfa.info/test-suite/test-cases/0001.sparql",
          "expectedResults": true,
          "hostLanguages": ["html4","html5","xhtml1","xhtml5","xml"],
          "versions": ["rdfa1.0","rdfa1.1"]
        }
      ]
    }
    

Other than the encapsulating elements, this looks pretty similar to the Turtle representation. There are a couple of differences though: instead of `dc:title`, we use the term `description`, instead of `rdfatest:hostLanguage`, we use `hostLanguages`. How are these related? The key is looking at the `@context` value. Looking at <http://rdfa.info/contexts/rdfa-test.jsonld>, we see the following:

    {
      "@context": {
        "dc":         "http://purl.org/dc/terms/",
        "xsd":        "http://www.w3.org/2001/XMLSchema#",
        "rdfatest":   "http://rdfa.info/vocabs/rdfa-test#",
        "test":       "http://www.w3.org/2006/03/test-description#",
    
        "classification": {"@id": "test:classification"},
        "contributor":    {"@id": "dc:contributor"},
        "description":    {"@id": "dc:title"},
        "expectedResults":{"@id": "test:expectedResults",
                           "@type": "xsd:boolean"},
        "hostLanguages":  {"@id": "rdfatest:hostLanguage",
                           "@container": "@set"},
        "input":          {"@id": "test:informationResourceInput",
                           "@type": "@id"},
        "num":            {"@id": "rdfatest:num"},
        "purpose":        {"@id": "test:purpose"},
        "versions":       {"@id": "rdfatest:rdfaVersion",
                           "@container": "@set"},
        "reference":      {"@id": "test:specificationReference"},
        "results":        {"@id": "test:informationResourceResults",
                           "@type": "@id"}
      }
    }
    

The context does exactly that: it provides a context for interpreting JSON data. Note the definition of `hostLanguages`: this indicates that `hostLanguages` is a _term_ definition, meaning that the term is replaced with the `@id` value, in this case `rdfatest:hostLanguage`, the same as used in Turtle. Both of these expand to an equivalent IRI <http://rdfa.info/vocabs/rdfa-test#hostLanguage>. In RDF, and in Linked Data in general, everything is described as a resource, either an IRI, a Literal or a Blank Node (basically a variable representing something we don&#8217;t know or don&#8217;t want to identify). The `"@container": "@set"` bit just says to expect that the value of `hostLanguages` will always be an array, to make processing more convenient.

Because we use _terms_ in JSON Object _key_ positions, this means that access from JavaScript can be quite convenient. Taking a look at the test suite Test model description, we can download the Manifest with an Ajax request and access elements using &#8216;.&#8217; notation, such as the following:

    var filteredTests = _.filter(this.loadedData, function(data) {
      return _.include(data.versions, version) &&
             _.include(data.hostLanguages, hostLanguage);
    });
    

Another advantage in using JSON is that the parse time is negligible. The manifest has about 3000 triples, which can actually take a while to parse as Turtle, but opening and parsing the JSON document is substantially faster.

As with many modern Web applications, the [RDFa Test Suite](http://rdfa.info/test-suite/) is a single-page application that uses Ajax calls to communicate with the server. The first call is to retrieve the [JSON manifest](http://rdfa.info/test-suite/manifest.jsonld). Subsequent calls retrieve test results, also expressed as JSON. The manifest is used to populate a [Backbone.js](http://backbonejs.org/) _Collection_. When a specific _version_ and _hostLanguage_ is selected, this collection is filtered to show only relevant tests, as is described in the previous example. The _Collection_ then drives a view element, which instantiates a view for each model to be tested.

## Collating Test Results

The second area where [JSON-LD](http://json-ld.org/) is used within the [RDFa Test Suite](http://rdfa.info/test-suite/) is for collating test results. After running a series of tests, a test user can generate [EARL](http://www.w3.org/TR/EARL10-Schema/) test results. Being an RDFa test suite, this report is naturally expressed in [RDFa](http://rdfa.info/). Here the [Backbone.js](http://backbonejs.org/) view technology comes in to play, since it is easy to use an HTML template to generate individual results, with the RDFa markup backed into the template.

The basic EARL template looks like the following:

    <script id='earl-item-template' type='text/template'>
      <h4>
        [
         <span property='rdfatest:rdfaVersion'><%= version %></span>
         <span property='rdfatest:hostLanguage'><%= hostLanguage %></span>
        ]
        Test <%= num %>:
        <span property='dc:title'><%= description %></span>
        <span property='earl:mode' resource='earl:automatic' />
      </h4>
      <p property='dc:description'><%= purpose %></p>
      <div class='property processorURL resource detailsURL'
           typeof='earl:Assertion'>
        <span property='earl:assertedBy' resource='' />
        <span class='resource processorURL' property='earl:subject' />
        <span class='resource docURL' rel='earl:test' />
        <p property='earl:result' typeof='earl:TestResult'>
          Result:
          <strong class='resource outcome'
                  property='earl:outcome'
                  resource=''><%= result %></strong>
        </p>
      </div>
    </script>
    

The _Earl view_ uses this template to generate a report for an individual test entry and fills in attribute or content values from within the view:

    var EarlItemView = Backbone.View.extend({
      template: _.template($('#earl-item-template').html()),
    
      render: function () {
        var JSON = this.model.toJSON();
        JSON.processorURL = this.options.processorURL;
    
        this.$el.html(this.template(JSON));
        this.$el.attr("resource", this.model.docURL());
        this.$(".property.processorURL")
          .attr("property",JSON.processorURL);
        this.$(".resource.processorURL")
          .attr("resource", JSON.processorURL);
        this.$(".resource.detailsURL")
          .attr("resource", this.model.detailsURL());
        this.$(".resource.docURL")
          .attr("resource", this.model.docURL());
        this.$(".resource.outcome")
          .attr("resource", 'earl:' +
                            this.model.get('result').toLowerCase());
        return this;
      }
    });
    

The result is a test result for a specific processor with a specific RDFa version and host-language. You can see an example report [here](http://rdfa.info/earl-reports/rdf.rb-rdfa1.1-html5.html).

However, this is not the end of it; to exit the W3C Candidate Recommendation phase, it&#8217;s necessary to have at least two interoperable implementations. What is needed, then, is a collated report that combines the output from several different processors into a single report. Because each individual report is an information resource representing a specific RDF graph, we can parse all of these documents into a single graph. But, to generate an HTML result, it would be convienent to have all the data available in a format convenient to use with Ruby [Haml](http://haml-lang.com/).

This is where [JSON-LD](http://json-ld.org/) use in languages like Ruby come to play. Ruby has great libraries for working with JSON, which basically transforms the JSON to a combination of Ruby native Hash, Array, String, Number and Boolean values. A [JSON-LD](http://json-ld.org/) representation a test assertion entry looks like the following:

    {
      "@id": "http://rdfa.info/test-suite/test-details/rdfa1.1/...",
      "@type": "earl:Assertion",
      "assertedBy": "http://rdfa.info/test-suite/",
      "test": "http://rdfa.info/test-suite/test-cases/rdfa1.1/html5/0001.html",
      "subject": "http://rubygems.org/gems/rdf-rdfa",
      "result": {
        "@type": "earl:TestResult",
        "outcome": "earl:pass"
      }
    }
    

Transforming this to Ruby gives essentially the exact same representation, so we can iterate over this using Ruby [Haml](http://haml-lang.com/). The natural thing to do is see how we can represent EARL test results through a hierarchical test structure.

As it happens, the EARL representation is not actually ideal. Each assertion is listed with a subject that indicates the specifics of the processor, test, version and host language. It indicates that it is asserted by the test suite, the test being run, the processor being tested, and the result of this test. However, I&#8217;d like to show the results in a tabular form, with the test suite at the top, followed by sections for each version and host language, and a table with a row for each generic test and a column for each processor. A typical result looks like the following:

<table class="report" style="background-color: #EEE;">
  <tr>
    <th>
      Test
    </th>
    
    <th>
      <a href="https://github.com/niklasl/clj-rdfa">clj-rdfa</a>
    </th>
    
    <th>
      <a href="https://github.com/rdfa/librdfa">librdfa</a>
    </th>
    
    <th>
      <a href="http://www.w3.org/2012/pyRdfa">pyRdfa</a>
    </th>
    
    <th>
      <a href="http://rubygems.org/gems/rdf-rdfa">RDF::RDFa</a>
    </th>
  </tr>
  
  <tr resource="http://rdfa.info/test-suite/test-cases/rdfa1.1/xhtml1/0001.xhtml" typeof="earl:TestCase">
    <td>
      <a href="#test_0001" property="rdfatest:num">0001</a><br /> <span property="dc:title">Predicate establishment with @property</span> 
      
      <link href="earl:automatic" property="earl:mode" />
    </td>
    
    <td class="PASS" rel="http://rdfa.info/test-suite/test-details/rdfa1.1/xhtml1/0001?rdfa-extractor=http%3A//clj-rdfa.herokuapp.com/extract.ttl%3Furl%3D" typeof="earl:Assertion">
      <link href="http://rdfa.info/test-suite/" property="earl:assertedBy" />
      
      <link href="http://rdfa.info/test-suite/test-cases/rdfa1.1/xhtml1/0001.xhtml" property="earl:test" />
      
      <link href="https://github.com/niklasl/clj-rdfa" property="earl:subject" />
      
      <span property="earl:result" resource="earl:pass" typeof="earl:TestResult" style="color: green"><br /> PASS<br /> </span>
    </td>
    
    <td class="PASS" rel="http://rdfa.info/test-suite/test-details/rdfa1.1/xhtml1/0001?rdfa-extractor=http%3A//rdfa.digitalbazaar.com/librdfa/rdfa2rdf.py%3Furi%3D" typeof="earl:Assertion">
      <link href="http://rdfa.info/test-suite/" property="earl:assertedBy" />
      
      <link href="http://rdfa.info/test-suite/test-cases/rdfa1.1/xhtml1/0001.xhtml" property="earl:test" />
      
      <link href="https://github.com/rdfa/librdfa" property="earl:subject" />
      
      <span property="earl:result" resource="earl:pass" typeof="earl:TestResult" style="color: green"><br /> PASS<br /> </span>
    </td>
    
    <td class="PASS" rel="http://rdfa.info/test-suite/test-details/rdfa1.1/xhtml1/0001?rdfa-extractor=http%3A//www.w3.org/2012/pyRdfa/extract%3Furi%3D" typeof="earl:Assertion">
      <link href="http://rdfa.info/test-suite/" property="earl:assertedBy" />
      
      <link href="http://rdfa.info/test-suite/test-cases/rdfa1.1/xhtml1/0001.xhtml" property="earl:test" />
      
      <link href="http://www.w3.org/2012/pyRdfa" property="earl:subject" />
      
      <span property="earl:result" resource="earl:pass" typeof="earl:TestResult" style="color: green"><br /> PASS<br /> </span>
    </td>
    
    <td class="PASS" rel="http://rdfa.info/test-suite/test-details/rdfa1.1/xhtml1/0001?rdfa-extractor=http%3A//rdf.greggkellogg.net/distiller%3Fraw%3Dtrue%26in_fmt%3Drdfa%26uri%3D" typeof="earl:Assertion">
      <link href="http://rdfa.info/test-suite/" property="earl:assertedBy" />
      
      <link href="http://rdfa.info/test-suite/test-cases/rdfa1.1/xhtml1/0001.xhtml" property="earl:test" />
      
      <link href="http://rubygems.org/gems/rdf-rdfa" property="earl:subject" />
      
      <span property="earl:result" resource="earl:pass" typeof="earl:TestResult" style="color: green"><br /> PASS<br /> </span>
    </td>
  </tr>
</table>

To take advantage of [JSON-LD](http://json-ld.org/) chaining, we really want a data structure that we can easily iterate on. By adding some extra markup to the report, we can do this using [JSON-LD](http://json-ld.org/) _Framing_, basically a query language for [JSON-LD](http://json-ld.org/) that allows us to change the data into a format we want to use. The frame document allows us to specify how we&#8217;d like our output. An abbreviated example is the following:

    {
      "@context": "http://rdfa.info/contexts/rdfa-earl.jsonld",
      "@type": "earl:Software",
      "rdfa1.1": {
        "@type": "rdfatest:Version",
        "html5": [{"@type": "earl:TestCase"}]
      }
    }
    

This says show items of type `earl:Software` with a property (associated with the version), referencing an object of type `rdfa:Version`, which has a property for each host language, which references a list of `earl:TestCase` items. This gives us a [JSON-LD](http://json-ld.org/) snippet such as the following:

    {
      "@context": "http://rdfa.info/contexts/rdfa-earl.jsonld",
      "@id": "http://rdfa.info/test-suite/",
      "@type": [
        "earl:Software",
        "doap:Project"
      ],
      "homepage": "http://rdfa.info/",
      "name": "RDFa Test Suite",
      "rdfa1.1": {
        "@type": "rdfatest:Version",
        "html5": [
          {
            "@id": "http://rdfa.info/test-suite/test-cases/rdfa1.1/html5/0001.html",
            "@type": "earl:TestCase",
            "num": "0001",
            "title": "Predicate establishment with @property",
            "description": "Tests @property ...",
            "mode": "earl:automatic",
            "http://rubygems.org/gems/rdf-rdfa": {
              "@id": "http://rdfa.info/test-suite/...",
              "@type": "earl:Assertion",
              "assertedBy": "http://rdfa.info/test-suite/",
              "test": "http://rdfa.info/test-suite/test-cases/rdfa1.1/html5/0001.html",
              "subject": "http://rubygems.org/gems/rdf-rdfa",
              "result": {
                "@type": "earl:TestResult",
                "outcome": "earl:pass"
              }
            },
            "http://www.w3.org/2012/pyRdfa": { "@type": "earl:Software", ... },
            "https://github.com/niklasl/clj-rdfa": { "@type": "earl:Software", ... },
            "https://github.com/rdfa/librdfa": { "@type": "earl:Software", ... },
            "https://github.com/rdfa/librdfa": { "@type": "earl:Software", ... },
            "http://rubygems.org/gems/rdf-rdfa": { "@type": "earl:Software", ... }
          }
        ]
      }
    }
    

We&#8217;ve basically wrapped each individual test case in a structure that inverts the information contained within the test case. Now we can use this within a [Haml](http://haml-lang.com/) template to create the HTML we&#8217;re interested in.

To see the complete [EARL](http://www.w3.org/TR/EARL10-Schema/) report, look [here](http://rdfa.info/earl-reports/).

## Conclusions

[JSON-LD](http://json-ld.org/) is the right technology for dealing with RDF and Linked Data in Web applications. It has a convenient representation for working from within various programming languages, such as JavaScript and Ruby. It&#8217;s use in implementing that [RDFa Test Suite](http://rdfa.info/test-suite/) proves it&#8217;s worth as a complementary technology for working with Linked Data on the Web along with [RDFa](http://rdfa.info/).

Next up, [we talk about the Distributed Denial of Service attack against the test suite](http://greggkellogg.net/2012/03/30/browserid-vs-ddos) and how we solved this very easily and quickly using [BrowserID](https://browserid.org/).
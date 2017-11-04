---
id: 161
title: A new RDFa Test Harness
date: 2012-03-18T00:00:12+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=161
permalink: /2012/03/a-new-rdfa-test-harness/
categories:
  - JavaScript
  - Ruby
  - Semantic Web
---
<span itemprop="name" style="display:none">A new RDFa Test Harness</span>
  
<span itemprop="description">Implementing the RDFa Test Suite as a modern Web application using Sinatra, Backbone.js and Bootstrap.js.</span>

Recently, [RDFa](http://rdfa.info/) entered the Candidate Recommendation phase for releasing
  
[RDFa Core 1.1](http://www.w3.org/TR/rdfa-core/), [RDFa 1.1 Lite](http://www.w3.org/TR/rdfa-lite/), and [XHTML+RDFa 1.1](http://www.w3.org/TR/xhtml-rdfa/) as W3C Standards.
  
I've been using RDFa for a couple of years, originally as part of the [Connected Media Experience](http://connectedmediaexperience.org/),
  
and lately because
  
I've become passionate about the Semantic Web. For the last 10 months, or so, this has extended to my becoming an Invited Expert
  
in the W3C, where I've worked on RDFa, [HTML microdata](http://www.w3.org/TR/microdata-rdf/) and [JSON-LD](http://json-ld.org/).

This is an introductory blog post on the creation of a new [RDFa Test Suite](http://rdfa.info/test-suite/). Here we discuss the use of
  
[Sinatra](http://www.sinatrarb.com/), [Backbone.js](http://backbonejs.org/) and [Bootstrap.js](http://twitter.github.com/bootstrap/index.html) to run the test suite. Later will come articles on the usefulness
  
of JSON-LD as a means of driving a test harness, [generating test reports](http://greggkellogg.net/2012/03/20/the-use-of-json-ld-in-the-rdfa-test-harness),
  
and the use of [BrowserID](https://browserid.org/) to deal with [Distributed
  
Denial of Service attacks](https://plus.google.com/102122664946994504971/posts/QNFm43sbokn) that cropped up overnight.

## RDFa Test Suite

Along with other RDF parsers and serializers (see sidebar), I have an [RDFa parser and serializer](http://rubygems.org/gems/rdf-rdfa).
  
In implementing the parser, and while working on [new features for RDFa 1.1](http://ivan-herman.name/2011/12/16/where-we-are-with-rdfa-1-1/),
  
the [RDFa Test Suite](http://rdfa.info/test-suite/) has been an invaluable resource. In my testing, I would use the
  
test manifest, describing the sets of inputs and expected outputs in the form of a [SPARQL ASK query](http://www.w3.org/TR/rdf-sparql-query/#ask).

A basic RDFa test is a small amount of markup intended to test a single feature.

    <!DOCTYPE html>
    <html prefix="dc: http://purl.org/dc/elements/1.1/">
    <head>
       <title>Test 0001</title>
    </head>
    <body>
      <p>
        This photo was taken by
        <span class="author"
              about="photo1.jpg"
              property="dc:creator">Mark Birbeck</span>.</p>
    </body>
    </html>
    

In this example, we're testing that the `@about` attribute sets the _subject_, `@property` sets the _property_ and the
  
text content sets the _object_ of a single RDF statement. Rendered as Turtle, it would look like the following:

    @prefix dc: "http://purl.org/dc/elements/1.1/" .
    <photo1.jpg> dc:creator "Mark Birbeck" .
    

A query to test this looks like the following:

    PREFIX dc: <http://purl.org/dc/elements/1.1/>
    ASK WHERE {
        <http://rdfa.info/test-suite/test-cases/rdfa1.1/html5/photo1.jpg>
          dc:creator "Mark Birbeck" .
    }
    

Note that the relative IRI in the `@about` is expanded relative to the document location, as is tested in the SPARQL query.

Using the test suite requires a publicly available endpoint, for which I released the [RDF Distiller](http://rdf.greggkellogg.net/distiller)
  
to test my implementation.
  
The test suite works with a provided URL, which invokes the processor with a test document. Basically, it does the following:

  1. The Web application performs a `GET` on the `/test-suite/check-test/:version/:suite/:num` service URL along
  
    with the processor endpoint as a query parameter.
  2. The service invokes the processor endpoint passing the URL of the test document.
  3. The processor then parses that document
  
    and returns a result in a different RDF format (for example [Turtle](http://www.w3.org/TR/turtle/) or [RDF/XML](http://www.w3.org/TR/REC-rdf-syntax/)).
  4. The processor parses the returned RDF document into a graph, and performs a SPARQL query against that graph.
  5. The result is a `true` or `false` value, which determines if the test passes or not.
  6. The result is formatted as JSON and returned the Web application.
  7. The Web application updates the test status in the UI.
  8. If running all tests, the completion event triggers the next test to run.

## Sinatra

[Sinatra](http://www.sinatrarb.com/) is a great lightweight framework for deploying simple Ruby applications on the web. The needs of this application,
  
while requiring a lot of different libraries, were really fairly simple. Basically, return a page listing the various tests,
  
respond to requests for test case source documents, activate a test with a specified processor endpoint and return the results.

The basic setup of the app is fairly straight forward:

    # Return the test suite driver page
    get '/test-suite/' do
      haml :test_suite
    end
    
    # Return a particular test, or SPARQL query
    get '/test-suite/test-cases/:version/:host_language/:num' do
      source = File.open(File.expand_path("../tests/#{num}.html"))
      case host_language
      when 'xhtml'
        # do XHTML-specific formatting of the test
      when 'html'
        # do HTML-specific formatting of the test
      when 'xml'
      when 'svg'
      end
    end
    
    # Run a test
    get '/test-suite/check-test/:version/:suite/:num' do
      # Get the SPARQL query
      source = File.open(File.expand_path("../tests/#{num}.sparql"))
    
      # Do host-language specific modifications of the SPARQL query.
      query = SPARQL.parse(source)
    
      # Invoke the processor and retrieve results, parsed into an RDF graph
      graph = RDF::Graph.load(params['rdfa-extractor'] + test_path(version, suite, num, format))
    
      # Run the query
      result = query.execute(graph)
    
      # Return results as JSON
      {:status => result}.to_json
    end
    

Backing up the Sinatra application are a number of Ruby Gems for working with [Linked Data](http://rubygems.org/gems/linkeddata) and [SPARQL](http://rubygems.org/gems/sparql). In
  
addition to reading and writing RDFa, there are gems for managing RDF graphs, reading other formats, such as [Turtle](http://www.w3.org/TR/turtle/)
  
and [RDF/XML](http://www.w3.org/TR/REC-rdf-syntax/), and running the SPARQL queries.

Driving the test suite is an Web application built using [Backbone.js](http://backbonejs.org/) and [Bootstrap.js](http://twitter.github.com/bootstrap/index.html).

## Backbone.js

Backbone is a JavaScript model-viewer-controller framework for building responsive applications in
  
JavaScript. It encourages building modular applications split into multiple classes with weak interdependencies. Models and
  
Collections are used to maintain application state, and reflect information from a server. The RDFa test suite has
  
two main models and a collection.

The Version model keeps track of information about what is being run. This includes the RDFa version and host language being tested
  
along with the current processor endpoint. It looks something like the following:

    window.Version = Backbone.Model.extend({
      defaults: {
        processorURL: "http://www.w3.org/2012/pyRdfa/extract?uri=",
        processorName: "pyRdfa",
        processorDOAP: "http://www.w3.org/2012/pyRdfa",
    
        // List of processors
        processors: {}
      }
    
      // Appropriate host languages for the current version
      hostLanguages: function() {
        return {
          "rdfa1.0": ["SVG", "XHTML1"],
          "rdfa1.1": ["HTML4", "HTML5", "SVG", "XHTML1", "XHTML5", "XML"],
          "rdfa1.1-vocab": ["HTML4", "HTML5", "SVG", "XHTML1", "XHTML5", "XML"]
        }[this.get("version")];
      }
    });
    

The Test model, uses the [test manifest](http://rdfa.info/test-suite/manifest.ttl) to instantiate a number of
  
Test model instances. Changing information in the Version model causes different tests to be enabled or disabled,
  
as appropriate for the given RDFa version and host language. It also affects URL generation for retrieving and running
  
different tests. In addition to instantiating tests, the Test Collection also allows the complete sequence of tests
  
to be run, by listening to an event for a completion event from running a test on the first test model and initiating
  
the test of the next.

## Styling the User Interface

I'm no designer, but I like a good looking and efficient user interface. Fortunately, the people at Twitter do too,
  
and they released [Bootstrap.js](http://twitter.github.com/bootstrap/index.html) as a means of tackling common problems. I won't go into detail here, but check out
  
their [example page](http://twitter.github.com/bootstrap/examples.html) to get an idea of the things you can do with Bootstrap.
  
What I immediately noticed about it is that I didn't really need to worry about layout. Note that you can even run
  
the Test Suite from an iPhone!

## Data Driven Tests

Of course, returning the test suite HTML is just part of the problem, we also need to get details about each test to
  
the page, so that it can respond to requests to run specific tests. The tests are managed through a
  
[test manifest](http://rdfa.info/test-suite/manifest.ttl), which is kept in [Turtle](http://www.w3.org/TR/turtle/) format to make it easy to add
  
tests. A typical entry looks like the following:

    <test-cases/0001> a test:TestCase;
       dc:title "Predicate establishment with @property";
       rdfatest:rdfaVersion "rdfa1.0", "rdfa1.1";
       rdfatest:hostLanguage "xml", "xhtml1", "html4", "html5", "xhtml5";
       test:classification test:required;
       test:informationResourceInput <test-cases/0001.html>;
       test:informationResourceResults <test-cases/0001.sparql> .
    

The basically describes an IRI for the test, in this case `test-cases/0001` relative to the location of the test suite,
  
the title of the test, the RDFa versions and host languages it applies to and a reference to the input and result documents.
  
RDFa has over 200 such tests defined. This is all well and good, but requiring yet another data format is an added complication.
  
Better to have the tests defined in a format more appropriate for use within an Web application, such as JSON. As it happens
  
[JSON-LD](http://json-ld.org/) is another specification that is still underway, but proving to be quite flexible and useful for our needs. For a
  
peek at the JSON-LD version of the RDFa test suite manifest, look [here](http://rdfa.info/test-suite/manifest.json).
  
More on using JSON-LD, and why It's such a good match for RDFa in the [next post](http://greggkellogg.net/2012/03/20/the-use-of-json-ld-in-the-rdfa-test-harness).
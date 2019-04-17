---
id: 165
title: BrowserID versus DDOS
date: 2012-03-30T15:58:46+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=165
permalink: /2012/03/browserid-versus-ddos/
categories:
  - JavaScript
  - Ruby
  - Semantic Web
---
<span itemprop="name" style="display:none"><a href="https://browserid.org/">BrowserID</a> vs DDOS</span>
  
<span itemprop="description" style="display:none">How <a href="https://browserid.org/">BrowserID</a> saved the <a href="http://rdfa.info/test-suite/">RDFa Test Suite</a> from a DDOS (Distributed Denial of Service Attack).</span>

This article is the third in a three-part series on implementing the [RDFa
  
Test Suite](http://rdfa.info/test-suite/). The [first
  
article](https://greggkellogg.net/2012/03/18/a-new-rdfa-test-harness)
  
discussed the use of [Sinatra](http://www.sinatrarb.com/), [Backbone.js](http://backbonejs.org/) and [Bootstrap.js](http://twitter.github.com/bootstrap/index.html) in
  
creating the test harness. The [second
  
article](https://greggkellogg.net/2012/03/20/the-use-of-json-ld-in-the-rdfa-test-harness)
  
discussed the use of JSON-LD. In this article, we focus on our use of
  
[BrowserID](https://browserid.org/) in responding to a Distributed Denial of Service Attack
  
(DDOS).

### RDFa Test Suite

Working on the updated [RDFa Test Suite](http://rdfa.info/test-suite/) has really been a lot of fun.
  
It was a great opportunity to explore new Web application technologies,
  
such as [Bootstrap.js](http://twitter.github.com/bootstrap/index.html) and [Backbone.js](http://backbonejs.org/). The test suite is a
  
single-page web application which uses a [Sinatra](http://www.sinatrarb.com/) based service to run
  
individual test cases.

The site was becoming stable, and we were starting to flesh out more test
  
cases for odd corner cases, when the site started to not respond. [Manu
  
Sporny](http://manu.sporny.org/), who's company [Digital
  
Bazaar](http://digitalbazaar.com/) is kindly donating hosting for the web
  
site, noticed that there were a number of Ruby processes that were
  
consuming available Ruby workers, and causing new requests to
  
block. The service _is_ fairly resource intensive, as it must invoke an
  
external processor and run a [SPARQL](http://www.w3.org/TR/rdf-sparql-query/) query over the results for each
  
test. It seemed as if the site was being hammered by a large number of
  
overzealous search crawlers! Naturally, we put a robots.txt in place,
  
expecting that conforming search engines would detect the site's crawl
  
preferences and back off, but that didn't happen. Upon further examination
  
of the server logs, we noted requests were streaming in from all over the
  
world! Clearly, we were under attack. (Who might wish ill of the RDFa
  
development effort? Who knows, but most likely this was just an anonymous,
  
and not specifically malicious attack).

My first thought was to make use a secret _api token_, configured into the
  
server and the web app, but that didn't really do the trick either; it
  
seemed that modern day malware actually just executes the JavaScript, so
  
it picks up the API key naturally!

### BrowserID to the Rescue!

Okay, how about authentication? It's typically a pain, and we were
  
reluctant to put up barriers in front of people who might want to test
  
their own processors or see how listed processors perform. The two current
  
contenders are [WebID](http://www.w3.org/2005/Incubator/webid/wiki/Main_Page) and [BrowserID](https://browserid.org/).

[WebID](http://www.w3.org/2005/Incubator/webid/wiki/Main_Page) has the laudable goal of combining personally maintained profile
  
information with SSL certificates (it was previously known as FOAF+SSL).
  
Basically, It's a mechanism to allow users to use a _profile page_ as
  
their identity. This could come off of their blog, Facebook, Twitter or
  
other social networking site. By configuring an SSL certificate into the
  
browser and pointing to their profile page, a service can determine that
  
the profile page actually belongs to the user. (There's much more to it,
  
you can read more in the [WebID
  
Spec](http://www.w3.org/2005/Incubator/webid/spec/)). A key advantage here
  
is that the service now has access to all of the self-asserted information
  
the user want's to provide about themselves as defined in their profile
  
page, such as `foaf:name`, `foaf:knows`, and so forth. The chief downside
  
is that the common source of existing user identities in the world haven't
  
bought into this, and there's a competing solution that offers similar
  
benefits.

[BrowserID](https://browserid.org/) is a Mozilla initiative to enable people with e-mail
  
addresses to use those e-mails to login to websites, kind of like
  
\[OpenID\]\[\] &#8211; only more secure. Basically, as I understand it, a service
  
wanting to support this would include the [BrowserID](https://browserid.org/) JavaScript client
  
code in their application and use a simple _Sign In_ button that invokes
  
this code. That sends a request off the the identity provider (IDP) to
  
authenticate the user, which has probably already happened in the past and
  
maintained in a cookie. The IDP then sends a response which invokes a
  
callback. The client then does a call back to the service to complete the
  
login passing the assertion.

The beauty is, using a tool such as the [sinatra-browserid](http://rubygems.org/gems/sinatra-browserid) Ruby gem,
  
this becomes dirt simple! Basically, on the API side, put in a call to
  
`authorized?` to determine if the user is authorized. If not, either
  
direct them to a login screen, or in the case of the [RDFa Test Suite](http://rdfa.info/test-suite/),
  
place an informational message telling them why we need them to login, and
  
identify the [BrowserID](https://browserid.org/) button at the top of the page.

In the principle entry-point to the test suite on the service side is
  
`/test-suite/check-test/:version/:suite/:num`. The only real change to
  
this method was to check for authorization before performing the test.

    # Run a test
    get '/test-suite/check-test/:version/:suite/:num' do
      return [403, "Unauthorized access is not allowed"] unless authorized?
    
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
    

In the banner, we add a little bit of Haml:

    ...
    %div.navbar-text.pull-right
      - if email
        %p.email
          Logged in as
          %span.email
            = email
          %a{:href => '/test-suite/logout'}
            (logout)
      - else
        = render_login_button
    

When the page is returned, the `email` variable is set if the user is
  
authorized, so they'll see the email address if they've authenticated, and
  
a login button otherwise. The `render_login_button` has handled entirely
  
by [sinatra-browserid](http://rubygems.org/gems/sinatra-browserid); no muss, no fuss!

The only other thing to do is to not show the test cases in the test
  
suite, unless the user has authenticated, which we can tell because
  
`$("span.email")` won't be empty. In our application.js, we use this to
  
either show the tests, or an explanation:

    // If logged in, create primary test collection and view
    if ($("span.email").length > 0) {
      this.testList = new TestCollection([], {version: this.version});
      this.testList.fetch();
      this.testListView = new TestListView({model: this.testList});
    } else {
      this.unauthorizedView = new UnauthorizedView();
    }
    

That's pretty much all there is too it. The only complication I faced is
  
that, when developing with _shotgun_, the session ID is changed with each
  
invocation, so it wasn't remembering the login. By fixing the session
  
secret this problem went away. Total time from discovery of the problem to
  
deployed solution: about 1 hour. Not too bad.

It's important to note that the [RDFa Test Suite](http://rdfa.info/test-suite/) is stateless, and we
  
don't really need any personal information; we don't collect information
  
anywhere, even in our logs. [BrowserID](https://browserid.org/) basically becomes a gate keeper
  
to help ward off abuse. It imposes a very low barrier of entry, so it
  
doesn't interfere with people using the site anyway they choose.

I do miss other user asserted information, such as the user's name and
  
so-forth. [OpenID](http://openid.net/), another single-signon initiative
  
that has lost momentum lately, provides a [Simple Registration
  
Extension](http://openid.net/specs/openid-simple-registration-extension-1_0.html)
  
add-on that allows users to assert simple information such as `nickname`,
  
`mail`, `fullname` and so forth. IMO, the right way to do this is with
  
something like [FOAF](http://xmlns.com/foaf/spec/) or the [schema.org](http://schema.org/Person) Person class. Perhaps
  
[BrowserID](https://browserid.org/) will provide something like this in the future.
---
id: 79
title: RSpec soft failure for pending test cases
date: 2009-10-26T20:40:45+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=79
permalink: /2009/10/rspec-soft-failure-for-pending-test-cases/
categories:
  - Ruby
---
Recently, I've been playing with RSpec to run [RDFa test cases](http://github.com/msporny/rdfa-test-suite). The suite has accepted and unreviewed test cases. My general way of testing is to parse the suite, and run each test through a generated spec as follows:

    test_cases.each do |tc|
      specify "test #{tc.name}" do
        tc.run_test do |input|
          RdfaParser::RdfaParser.new.parse(input, tc.informationResourceInput)
        end
      end
    end
    

The problem is, some tests are pending, so that a failure should be soft, and not hard. The way to do this is to catch `Spec::Expectations::ExpectationNotMetError`. This this, we can modify the above test as follows:

    test_cases.each do |tc|
      specify "test #{tc.name}" do
        tc.run_test do |input|
          begin
            RdfaParser::RdfaParser.new.parse(input, tc.informationResourceInput)
          rescue Spec::Expectations::ExpectationNotMetError =&gt; e
            if tc.status == "unreviewed
              pending(e.message) {  raise }
            else
              raise
            end
          end
        end
      end
    end
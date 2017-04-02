---
id: 44
title: Observers in Rails 1.2
date: 2007-01-27T05:13:37+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=44
permalink: /2007/01/observers-in-rails-1-2/
categories:
  - Ruby
---
Thanks to [Rick Olson](http://weblog.techno-weenie.net) for providing assistance with the proper syntax for instantiating observers in Rails 1.2. The old way was to add an _observer_ statement to the appropriate controller, such as:

    observer :user_observer
    

Hidden in the ignored parts of `conf/environment.rb` was the proper notation:

    # Activate observers that should always be running
    # config.active_record.observers = :cacher, :garbage_collector
    

This makes the last of my deprecation messages go away!
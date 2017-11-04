---
id: 71
title: Detecting action caching within controller
date: 2009-08-30T01:12:19+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=71
permalink: /2009/08/detecting-action-caching-within-controller/
categories:
  - Ruby
---
Rails offers three forms of caching within your controller: _page_, _action_ and _fragment_. _Page caching_ results in the fastest access times, as the results of the first call to an action are saved in a file so that subsequent accesses never even hit rails. However, for most applications, this isn't useful, as there may be dynamic content on a page, and this does not allow for authentication. _Fragment caching_ is the most detailed, and allows different parts of a page to be cached and allows you to check for the presence of a cached fragment within your controller (or view), but it requires the most maintenance of cache keys. Action caching is a nice compromise between the two. It allows the controller to get into the action but takes care of cache key creation. It also allows for a dynamic layout using the `:layout` option. However, in some actions, the amount of work done by the controller may be non-trivial, so it would be nice to check for the presence of the cache within the body of the controller action. This can be solved by borrowing some code from within `ActionController::Caching::Actions`.

    def index
      cache_path = ActionCachePath.new(self, cache_key)
      return if self.read_fragment(cache_path.path)
      # body of action
    end
    

Controlling your own cache keys is also useful, particularly for actions that may take a number of parameters:

    def cache_key
      key = "#{params[:controller]}/#{params[:action]}"
      params.each_pair {|k, v| key += ":#{k}=#{v.gsub(/\s/, "_")}
      key
    end
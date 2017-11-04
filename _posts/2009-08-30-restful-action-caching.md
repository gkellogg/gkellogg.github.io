---
id: 74
title: Restful action caching
date: 2009-08-30T01:35:31+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=74
permalink: /2009/08/restful-action-caching/
categories:
  - Ruby
---
Caching actions without layout can be complicated when multiple request formats are used. In particular, an HTML response may use a dynamic layout, in which case you want to use the `:layout => false` option. However, other formats (such as XML) don't use a layout, but the `:layout => false` option to &#95;caches&#95;action_ does not properly cache the body in this case. To solve the problem, create two caches action statements:

    caches_action :show,
                  :if => lambda { |c| c.request.format == :html },
                  :cache_path => lambda { |c| c.cache_key },
                  :layout => false   caches_action :show,
                  :unless => lambda{ |c| c.request.format == :html },
                  :cache_path => lambda { |c| c.cache_key },
                  :layout => true
    

Also, relying on the accept header may not cause the action caching module to detect the appropriate format. Try this in your `application_controller`:

    before_filter :set_explicit_request_format
    def set_explicit_request_format
      # Set format explicitly from accept header, unless it&#39;s already set
      request.format = :html if request.format == :any
      params[:format] ||= request.format.to_sym.to_s
    end
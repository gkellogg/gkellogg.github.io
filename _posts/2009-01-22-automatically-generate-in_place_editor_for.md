---
id: 64
title: Automatically generate in_place_editor_for
date: 2009-01-22T06:21:03+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=64
permalink: /2009/01/automatically-generate-in_place_editor_for/
categories:
  - Ruby
---
Recently, I was creating some in\_place\_editors for a polymorphic controller I'm working on. Although the problems not particular to polymorphic controllers, I didn't want to embed too much model information within the controller. I came up with a way to use `meathod_missing` to define the `in_place_editor_for` on demand:

    class ResourcesController << ApplicationController
      def method_missing(method_id, *args)
        if method_id.to_s.match(/set_resource_(.*)$/) && @resource.respond_to?($1)
          property = $1
    
          # Defines "set_resource_#{property}" on the fly
          self.class.in_place_edit_for(:resource, property)
    
          # Call the newly defined method
          self.send("set_resource_#{property}", args)
        else
          super
        end
      end
    end
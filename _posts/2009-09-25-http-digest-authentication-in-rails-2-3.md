---
id: 69
title: HTTP Digest Authentication in Rails 2.3
date: 2009-09-25T20:37:47+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=69
permalink: /2009/09/http-digest-authentication-in-rails-2-3/
categories:
  - Ruby
---
After a fair amount of work, I'm happy to report that HTTP Digest Authentication is now a part of Rails 2.3. Although I put the finishing touches to get this into the release, it is based on work done by [Dan Manges](http://www.dcmanges.com/blog) and [Xavier Shay](http://rhnh.net/) . Also, thanks to [Don Parish](http://rails.lighthouseapp.com/users/32494) for bug fixes and improvements after original acceptance.

Read more about HTTP Digest Authentication in Rails 2.3 [Ryan's Scraps](http://ryandaigle.com/articles/2009/1/30/what-s-new-in-edge-rails-http-digest-authentication). Relevant Lighthouse entries: [1230](http://rails.lighthouseapp.com/projects/8994/tickets/1230-implement-http-digest-authentication), [1848](http://rails.lighthouseapp.com/projects/8994/tickets/1848), and [2000](http://rails.lighthouseapp.com/projects/8994/tickets/2000-patch-for-http-digest-authentication-uri-comparison). The last one includes a change, not yet approved for 2.3, which allows for using the HA1 part of the digest to store a hash of the password, rather than the cleartext of the original version. Hopefully, we'll get a version of that in soon. Also, the current implementation depends on using a session secret when computing the nonce. [2000](http://rails.lighthouseapp.com/projects/8994/tickets/2000-patch-for-http-digest-authentication-uri-comparison) proposes a way to avoid this so no session is required.

Hopefully. we'll see the open issues resolved and get this into a 2.3.1 update.

Gregg
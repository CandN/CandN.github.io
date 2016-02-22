---
layout: post
title: Rack::JSONP and Pusher.com invalid callback
category: Technical Notes
---
When you want to return actual JavaScript from your controller you might use `Rack::JSONP` middleware from [rack/rack-contrib](https://github.com/rack/rack-contrib) gem. It works well.

The thing is – if you're using [Pusher](http://pusher.com) like me, you'll get a `400 Bad Request` response every time.

Why is that?

Well, there's this regex which validates your callback parameter and appareantly it doesn't allow brackets nor quotes. Observe:

~~~ ruby
# gem regex VS Pusher.com default callback
Rack::JSONP::VALID_CALLBACK =~ "Pusher.auth_callbacks['1']"
=> nil
~~~

Dirty solution is simple: replace the `Rack::JSONP::VALID_CALLBACK` in the initializers, either to allow those brackets and quotes or allow this very specific callback from Pusher.

BTW **do not** allow anything /*/ or else you're creating a security risk of XSS attacks.

I'm not sure how active the repository is but I left an issue [#123](https://github.com/rack/rack-contrib/issues/123) there.

---
layout: post
title: UUIDs in Rails generators
---
You want to use UUIDs instead of incremental IDs as your primary keys – because it's a [good idea](http://andrzejonsoftware.blogspot.com/2013/12/decentralise-id-generation.html) – but Rails annoys you by still generating migration files with integers?

Then add this to configuration:

~~~ ruby
Rails.application.config.active_record.primary_key = :uuid
~~~

If you are not sure how to enable UUID extension in Rails, follow [this](http://blog.arkency.com/2014/10/how-to-start-using-uuid-in-activerecord-with-postgresql/) article from folks at Arkency.

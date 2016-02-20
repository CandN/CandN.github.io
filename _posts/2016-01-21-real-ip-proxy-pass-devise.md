---
layout: post
title: Real IP with Devise and nginx proxy_pass
category: Technical Notes
---
Sometimes you might use a `proxy_pass` to run both front-end and the back-end of your service on the same machine. Sometimes you pass that traffic somewhere else, no matter.

Your nginx configuration might look like this:

~~~ text
location ~ ^/(api_path|admin_panel) {
  proxy_pass https://localhost;
  access_log off;
}
~~~

The thing is – if this API or admin panel handle user aunthentication, in Devise both `last_sign_in_ip` and `current_sign_in_ip` will always point to your localhost and if you need to track from where your users come from or need it as a security precaution then it isn't much good.

No matter if we check the `request.ip` or `request.remote_ip` – both values will return unhelpful data.

We can fix that by setting an additional proxy header:

~~~ text
location ~ ^/(api_path|admin_panel) {
  proxy_set_header X-Real-IP $remote_addr;
  proxy_pass https://localhost;
  access_log off;
}
~~~

Now we can check the `request.headers["X-Real-IP"]` and be done with it.

You can override `Trackable` model [here](https://github.com/plataformatec/devise/blob/master/lib/devise/models/trackable.rb#L23) or use your own callback.

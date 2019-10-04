---
layout: post
title:  "A very short intro to the Roda web toolkit"
date:   2019-10-04 13:00:00 -0300
categories: ruby webdev
---
## Introduction

There are several &quot;_lightweight_&quot; Ruby web &amp; API libraries out there.
_Sinatra_ is probably the most known but there are others that are newer, faster, cleaner, more flexible and/or better maintained.

One of those is [_Roda_](https://roda.jeremyevans.net), created by Jeremy Evans, the creator of the Sequel gem. It started as a Cuba fork but then evolved a lot. It is specially notably by its speed and plugin system, shared characteristics with its sibling Sequel gem, where everything can be overridden.

It is auto-defined as a &quot;_Routing tree web toolkit_&quot;. Why is that? Because of the way the routes are defined &amp; run. Let me show you an example:

```ruby
require "roda"

classApp < Roda
  plugin :render
  plugin :all\_verbs
  route do |r|
    r.root do
      view :index
    end
    r.is  "artist ", Integer do |artist\_id|
      @artist = Artist[artist\_id]
      check\_access(@artist)
      r.get do
        view :artist
      end
      r.post do
        @artist.update(r.params[ "artist "])
        r.redirect
      end
      r.delete do
        @artist.destroy
        r.redirect  "/ "
      end
    end
  end
end
```

If you compare with other libraries like Sinatra, instead of setting the @artist variable and checking the access is allowed in all three of the actions, the variables are set as soon as that branch of the tree is taken, and can be used in all routes under that branch. This is why Roda is called a routing tree web toolkit.

More examples of the routing flexibility:

```ruby
  route do |r|
    # array matchers
    r.on %w[hello hi]do |greeting|
      &quot;#{greeting}, world!&quot;
    end
    # method matchers
    r.is  "hello ", method: [:post, :put, :patch] do
      &quot;All your base are belong to us&quot;
    end
    # Regexp matchers
    r.on  "users "do
      r.get /(\d+)/do |user\_id|
        &quot;Your id is #{user\_id}&quot;
      end
    end
  end
```

Roda's plugin system design is shared with the Sequel gem but also copied in other projects, like in the [Shrine](https://shrinerb.com) file attachment toolkit, [Cuba(v3)](http://cyx.is/cuba-3-released.html).

You can start with a small, compact core of an application and grow complexity over time and learning as you go, habing fine grained control on features and performance.

For instance, wants [websockets](https://www.rubydoc.info/gems/roda/2.13.0/Roda/RodaPlugins/Websockets), checked:

```ruby
plugin :websockets

route do |r|
  r.get &quot;room&quot;do
    # Matches if it "s a websocket request
    r.websocket do |ws|
      ws.on(:message) { ... }
      ws.on(:close) { ... }
      # ...
    end
    # If the request is not a websocket request, we render a template
    view &quot;room&quot;
  end
end
```

Do you want a JSON API? The _json_ [plugin](https://www.rubydoc.info/gems/roda/Roda/RodaPlugins/Json) responds with JSON automatically for arrays &amp; hashes:

```ruby
plugin :json

route do |r|
  r.root do
    [1, 2, 3]
  end
  r.is &quot;foo&quot;do
    { "a "=\&gt; "b "}
  end
end
```

But you can customize it easily, for example, adding _ActiveRecord_ relations:

```ruby
plugin :json, classes: [Array, Hash, ActiveRecord::Base, ActiveRecord::Relation],
  serializer: proc { |object|
    case object
    when Array, Hash
      object.to\_json
    else
      Serializer.new(object).as\_json
    end
  }
route do |r|
  r.get &quot;albums/recent&quot;do
    Album.recent
  end
  r.get &quot;albums/:id&quot;do |id|
    Album.find(id)
  end
end
```

The complete list of [bundled plugins](http://roda.jeremyevans.net/documentation.html#included-plugins) is huge, more than [90](https://www.rubydoc.info/gems/roda/Roda/RodaPlugins), and there are several community extensions, so you are pretty much covered here. In case you want to do one, this is the structure:

```ruby
class Roda
  moduleRodaPlugins
    moduleMyPlugin
      moduleClassMethods
      end
      moduleInstanceMethods
      end
      moduleRequestClassMethods
      end
      moduleRequestMethods
      end
      moduleResponseClassMethods
      end
      moduleResponseMethods
      end
    end
    register\_plugin(C:my\_plugin, MyPlugin)
  end
end
```

## Performance

An image is worth a thousand words. Compare the request per second and the memory usage:

![](https://i.ibb.co/bvWRMqG/image1.png)

Pretty close to raw Rack values, but with a nicer DSL.

## Maintenance

Jeremy responses in the [mailing list](https://groups.google.com/forum/#!forum/ruby-roda) are quick &amp; he resolves any issues, too:

![](https://i.ibb.co/gSnnpzz/image2.png)

_(Look Ma, no issues)_

## Some disadvantages

- No routes introspection (although there is a [plugin](https://github.com/jeremyevans/roda-route_list) that helps with this).
- Less gems, as many are tightly coupled with default framework.
- Less community support vs Rails or Sinatra.
- Some reinventing the wheel may be unavoidable.
- The routing structure may not be your style (but there is a [plugin](http://roda.jeremyevans.net/rdoc/classes/Roda/RodaPlugins/ClassLevelRouting.html) to use a Sinatra like class route definitions)

## Extra links:

- This is a nice free [Roda book](https://fiachetti.gitlab.io/mastering-roda/).
- The official [mailing list](https://groups.google.com/forum/#!forum/ruby-roda).
- If you want to know more about this _plugin system pattern_, check [this post](https://twin.github.io/the-plugin-system-of-sequel-and-roda/) from the [Shrine](https://github.com/shrinerb/shrine) "s creator.
- Other benchmarks: [ruby web frameworks micro benchmarks](https://github.com/luislavena/bench-micro) project, the famous [TechEmpower web benchmarks](https://www.techempower.com/benchmarks/#section=data-r18&amp;hw=ph&amp;test=json&amp;l=zijxtr-f) (filtered for Ruby frameworks) and also [this project](https://github.com/jeremyevans/r10k/).
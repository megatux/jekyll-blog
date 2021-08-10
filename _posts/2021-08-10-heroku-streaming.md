---
layout: post
title: Heroku, I stream you!
date: 2021-08-10 17:25 -0300
tags: webdev ruby rails streaming heroku
---

The problem: timeouts on generated content exported as CSV or other formats.

It's a very common feature to have export functionality on the page to formats like CSV, XLSX, PDF, etc.

This is a simple thing on Rails inside a #respond_to:

```
def index
  @records = ...
  respond_to do |format|
    format.html # index.html
    format.csv { some_csv_generation_code }
  end
end
```

However, some day, errors start to appear from users when they try to download a lot of information.

The server responds with timeout errors after a certain amount of seconds and that stops the request that was generating the file. 

In a PaaS like **Heroku** this time limit is at 30 seconds of no content generated. Also there is another timeout of 55’’ between each new fragment of output. Citing the official information:

> An application has an initial 30 second window to respond with a single byte back to the client. However, each byte transmitted thereafter (either received from the client or sent by your application) resets a rolling 55 second window. If no data is sent during the 55 second window, the connection will be terminated.

Rails seems to have streaming functionality to handle this kind of output generation in the _ActionController::Live_. Also in Rails edge (future v7) there is a #send_stream method that seems to achieve this functionality so I copied it to my Rails 5.2 app into a controller concern and changed the CSV generation logic to implement the _Enumerable #each method_.
It was not working until I also override the _Last-Modified_ header. See [this issue discussion](https://github.com/rack/rack/issues/1619).

This is the full code of the controller concern:

```
require 'content_disposition'
 
module Streamable
  extend ActiveSupport::Concern
  include ActionController::Live
 
  # Almost verbatim copy of new Rails 7 method.
  # See https://edgeapi.rubyonrails.org/classes/ActionController/Live.html#method-i-send_stream
  def send_stream(filename:, disposition: 'attachment', type: nil)
    response.headers['Content-Type'] =
      (type.is_a?(Symbol) ? Mime[type].to_s : type) ||
      Mime::Type.lookup_by_extension(File.extname(filename).downcase.delete('.')) ||
      'application/octet-stream'
 
    response.headers['Content-Disposition'] = ContentDisposition.format(disposition: disposition, filename: filename)
      # with rails 6 remove content_disposition gem and use:
      #  ActionDispatch::Http::ContentDisposition.format(disposition: disposition, filename: filename)
 
    # Extra: needed for streaming
    response.headers['Last-Modified'] = Time.now.httpdate
 
    yield response.stream
  ensure
    response.stream.close
  end
end

```

For Rails 5.x I needed an extra gem to set the _Content-Disposition_ header:

`gem 'content_disposition', '~> 1.0' # Not needed on Rails 6`

A testing example of a controller that generates slow dummy data: 

```
class ExporterController < ApplicationController
  include Streameable

  def index
    respond_to do |format|
      format.html # index.html
      format.js   # index.js
      format.csv do
        send_stream(attachment_opts) do |stream|
          stream.write "email_address,updated_at\n"

          50.times.each do |i|
            line = "pepe_#{i}@acme.com,#{Time.zone.now}\n"
            stream.write line
            puts line
            sleep 1  # force slow response
          end
        end
      end
    end
  end

  private

  def attachment_opts
    {
      filename: "data_#{Time.zone.now.to_i}.csv",
      disposition: 'attachment',
      type: 'text/csv'
    }
  end
end
```

Finally, if you use something like curl you will see the output generated second by second.

> curl -i  http://localhost:3000/exporter

![Alt Text](https://i.ibb.co/4t4wWZ0/streaming-csv.gif)

Now your app can stream big amounts of data like a champ!. 

I have deployed this app in a [Heroku app](https://megatux-stream-test.herokuapp.com/exporter) and the code is on [this github repo](https://github.com/megatux/heroku_streaming_csv).

An extra performance tip. When using ActiveRecord to fetch tons of records from the DB consider using _#find_each_ instead of _#each_ so it will fetch the records in batches. 

Let me know if this was helpful for you.
Happy Rails hacking!

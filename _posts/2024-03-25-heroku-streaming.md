---
layout: post
title: Adding lazy loading of items using htmx
date: 2024-03-25 12:34 -0300
tags: webdev ruby rails tailwindcss phlex htmx
---

This is a continuation of some testing of these front-end libraries.
I used the [lazy load example here](https://htmx.org/examples/lazy-load/) to add lazy loading of the articles on this index page.

This is how it looks like:

![a spinning animation before showing articles](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/30qtx2vfu0sgqa59texu.gif)

_Previous to this change, I created an Articles Active Record model, ran migrations, and changed the insert action to create new records._

I added a GET _htmx_ definition in the index view code to fetch the items' partial and added a route and a controller action for this new response.

You can see that there is an extra logic in the `Articles::IndexView` to discern between displaying the lazy load code or the fragment with the list of articles. This is based on an initialization parameter. I'd probably refactor this later, maybe extracting the list block into a new component.

Here is the important code changes:

### Route definition

```ruby
  resources :articles do
    get "items", on: :collection
  end
```

### Controller changes

```ruby
  def index
    render Articles::IndexView.new(nil)
  end

  def items
    articles = Article.all.order(id: :desc)
    sleep 1 # this is to simulate a slow response and should be removed in a real scenario
    render Articles::IndexView.new(articles), layout: false
  end
```

### Changes in the index view class

```ruby
class Articles::IndexView < ApplicationView
...
  def template
    if @articles.nil?
      title_with_add_button
      load_indicator_placeholder
    else
      load_articles_section
    end
  end

  def load_indicator_placeholder
    div("hx-get": items_articles_path, "hx-trigger": "load", class: "flex justify-center") {
      img(alt: "Loading items...",
        class: "htmx-indicator", width: "150", src: "/bars.svg")
    }
  end

  def load_articles_section
    section(id: "article-list", class: "grid md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4") {
      articles.each { |el| render el }
    }
  end
```

Also available in the [GitHub repo](https://github.com/megatux/rails_sample_bun_tailwind_phlex_htmx)

Happy hacking!

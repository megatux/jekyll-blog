---
layout: post
title: Adding an inline delete action on the listing page
date: 2024-03-30 12:34 -0300
tags: webdev ruby rails tailwindcss phlex htmx
---

This is the part III of the Phlex, htmx combo for building UIs.

![deleting an article](https://i.ibb.co/R79NnTF/Grabacindepantalladesde2024-03-3013-38-28-ezgif-com-video-to-gif-converter.gif)

Pretty similar to the insert action,
it consist of:

- Edit ArticleComponent to add a delete button code.

- Add a delete route, if needed (not here).

- Add a controller method to handle the removal of the article using the _id_ received.

```ruby
  def template
    article(class: ARTICLE_STYLE, id: "article-#{@article.id}") {
      h3(class: HEADER_STYLE) {
        i { "#{@article.id} - #{@article.name}" }
      }
      p(class: "bg-slate-200 p-4 basis-3/4") { @article.details || "No description" }
        button(class: DELETE_BUTTON_STYLE,
          **hx(delete: article_path(@article), confirm: "Delete Article?", target: "#article-#{@article.id}", swap: "outerHTML swap:.5s")) {
          "X"
        }
      }
    }
  end
```
_The new button inside the Phlex Article component_

```ruby
  def destroy
    Article.delete params[:id]
    render plain: ""
  end
```
_The controller action_

```css
article.item-article.htmx-swapping {
    opacity: 0;
    transition: opacity 0.5s ease-out;
}
```
_Some CSS for htmx effect_

Note that it should return an empty string or it would not do the removal effect in the UI and JS would be needed.

It felt pretty easy to add this functionality. It start to looks good.

Next will be that inline edit action or some inline filtering of the items in the list.

Happy hacking!

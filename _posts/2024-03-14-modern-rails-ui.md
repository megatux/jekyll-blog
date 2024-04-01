---
layout: post2
title: Experimenting with Modern UI Alternatives in Rails
date: 2024-03-14 12:34 -0300
tags: webdev ruby rails tailwindcss phlex htmx
---

I embarked on an experiment with a Rails application using modern alternative tools and libraries. In this post, I'll walk you through the steps I took to set up a Rails template repository with the following technologies:

- Configuration with **[Bun JS](https://bun.sh/)** runtime for efficient JavaScript management.

- Integration of **[TailwindCSS](https://tailwindcss.com/)** for sleek UI design.

- Utilization of **[Phlex](https://www.phlex.fun/)** library to build views entirely in Ruby, eliminating the need for mixed template languages and replacing partials and view helpers with components.

- Implementation of **[HTMX](https://htmx.org/)** actions for dynamic content swapping without full page refresh.

### Setup Steps:

1. Installed bun js runtime (I used [mise](https://mise.jdx.dev/), btw)

2. Added necessary gems and generated some configurations:

{% gist 4b20c52314670fd5b392a77e0b689dba %}

3. Edited *Procfile* to remove bun css watch line and use *tailwindcss:watch* task.

4. Edited *application_layout.rb* to remove *javascript_importmap_tags* line.

### This is how it looks:

![application screen-shot showing some articles and an Add button](https://i.ibb.co/QH6xXCS/Grabaci-n-de-pantalla-desde-2024-03-14-23-20-58.gif)

![Server outputs](https://i.ibb.co/G9N8J04/Captura-desde-2024-03-12-19-49-16.png)

The Rails ***bin/dev*** command starts the three processes defined in the ***Procfile*** file. The Rails server, the Bun js runtime, and the TailwindCSS watcher.

### Some code highlights:

{% gist ea1054e0559fd805878456047509cdc0 %}

The main application layout, in Ruby, has some header and footer definitions and a call to a Ruby *block* in the <main> tag.

{% gist 8d0ac8cc15febde7b4bd35b443394e57 %}

The controller has an action for listing the Articles and the method to handle the POST to add a new Article.

{% gist bba3d09388a13bcfe5390bcfdf12949a %}

The Index view has a title, an Add button, and the list of Articles. In a real app these Articles would probably be passed on initialization from a DB query outside this class.

{% gist 5740835713bca329ebdf43c377f62a1a %}

The Article component just generates some random data. In a real app it would probably be populated from an Active Record object on initialization.

The full source repository [is available here](https://github.com/megatux/rails_sample_bun_tailwind_phlex_htmx).

By following these steps, I was able to leverage a modern UI frontend for a Rails application in a hopefully more maintainable way.

I plan to continue experimenting with these tools on more advanced topics like forms, websockets & SSE updates, filters, pagination, using JS interactivity, and more complex components.

Happy coding!
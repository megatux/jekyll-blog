---
layout: post
title:  "asdf version manager"
date:   2019-10-02 15:00:00 -0300
categories: tools development
---
# Asdf-vm

An extendable version manager

## Introduction

When you work as a developer, it is the norm to have projects made with different languages &amp; storages. Even if you only use a specific language or tool, every project could use a different version of it.

A version manager allows you to easily install, use and switch between several versions of the same language or program.

This is necessary mainly because most OS don&#39;t provide an effective way to have several versions of the same program or library (at a system level or per user account). For example, having several versions of the Ruby language at the same time in Linux with deb or rpm packaging systems.

To overcome this limitations some scripts or &quot;language version managers&quot; like chruby, _RVM_ or _rbenv_ for Ruby, _nvm_ &amp; _n_ for Node.js and dozens more started to appear. Each one with its own commands, setup instructions and peculiarities. Most of this version managers work installing all the things inside the user&#39;s home directory. As you can imagine, this may present some scaling &amp; usability issues.

Enter **asdf-vm**

_asdf_ is  &quot;generic&quot; version manager, that with same interface of commands &amp; one setup you can handle a lot of languages &amp; tools versions altogether and it is extendable via plugins.

Advantages, Why use asdf-vm?

- single CLI for multiple languages (&amp; others)
- consistent commands to manage all your languages
- single global config keeping defaults in one place
- single .tool-versions config file per project
- support for existing config files .node-version, .nvmrc, .ruby-version for easy migration
- automatically switches runtime versions as you traverse your directories
- simple plugin system to add support for your language of choice
- completion scripts managed by the plugin, not you!

The extensive list of plugins can be found [here](https://asdf-vm.com/#/plugins-all?id=plugin-list). As you can see, there are a lot of languages like Python, Ruby, Go, Node.js, PHP, Java, .NET Core, Rust, Lua; stores like SQLite, Postgres, Redis, MongoDB, MySQL; and tools like ElasticSearch, ImageMagick, RabbitMQ, Vault, Terraform, k9s. Also, it is pretty easy to [create a plugin](https://asdf-vm.com/#/plugins-create).

The install instructions for OSX &amp; Linux [are here](https://asdf-vm.com/#/core-manage-asdf-vm?id=install-asdf-vm). It supports Bash, ZSH, Fish. They are quite simple and involves downloading a git repository or using _brew_ and adding a couple of lines in your shell configuration file.

Example usage:

Let&#39;s say your new assigned project &quot;_Alpha_&quot; requires Ruby v2.4.2, Postgresql \&gt; v9.x, Redis 4.x, ElasticSearch 6.7 and ImageMagick tooling and you have the plugins for all except _imagemagick_ don&#39;t have anything of that installed. You could setup it with:

```
$ asdf install redis 4.0.14
$ asdf install postgres 9.6.15
$ asdf install ruby 2.4.2
$ asdf install elasticsearch 6.7.1
$ asdf plugin-add imagemagick
$ asdf install imagemagick 7.0.8-66
```

Then you setup the _local_ configuration for your project with:

```
$ asdf local redis 4.0.14
$ asdf local postgres 9.6.15
$ asdf local ruby 2.4.2
$ asdf local elasticsearch 6.7.1
$ asdf local imagemagick 7.0.8-66
```

This creates a &quot;.tool-versions&quot; file, that you can add to the version control system, with the rest of your project files.

If you switch to another project, when entering the directory, the local versions will be setup automatically.

Other command examples:

Just type &quot;asdf&quot; if you want to see the complete list of commands.

List all available plugins for installation:

   $ asdf plugin-list-all

List your currently installed plugins:

    $ asdf plugin-list

Installing a new plugin:

    $ asdf plugin-add elixir

What I am currently using (local or global):

    $ asdf current

Set a _global_ version for Ruby language:

    $ asdf global ruby 2.4.7

List your current installed versions for the Ruby plugin:

```
$ asdf list ruby
  2.4.7
  2.5.6
  2.6.4
  jruby-9.2.8.0
```

List the available to install Postgresql versions:

```
$ asdf list-all postgres
  1.08
  …CUTTED…..
  11.4
  11.5
````

A nice feature is &quot;Tab&quot; completion for commands &amp; version numbers, for instance, try typing

```
$ asdf <TAB><TAB>
current          install          local            plugin-list-all  reshim           update
global           list             plugin-add       plugin-remove    shell            where
help             list-all         plugin-list      plugin-update    uninstall        which
```

```
$ asdf install imagemagick <TAB><TAB>
7.0.8-38    7.0.8-41    7.0.8-44    7.0.8-47    7.0.8-50    7.0.8-53    7.0.8-56    7.0.8-59    7.0.8-62  
7.0.8-65    7.0.8-39    7.0.8-42    7.0.8-45    7.0.8-48    7.0.8-51    7.0.8-54    7.0.8-57    7.0.8-60  7.0.8-63    7.0.8-66    7.0.8-40    7.0.8-43    7.0.8-46    7.0.8-49    7.0.8-52    7.0.8-55    7.0.8-58  7.0.8-61    7.0.8-64    continuous
```

And that&#39;s basically it. In contrast to _Docker_, where you have an isolated filesystem, libraries, ports &amp; users and runs a specific process inside it utilizing the _docker_ command or _composing_ the application parts with some other tool like _docker-compose_, which is great for creating reproducible &amp; isolated environments for deploying, _asdf_ allows you a more natural &amp; lightweight development workflow.

Summarizing, _asdf_ tool lets me work without worry about conflicts between several moving parts, with speed and a minimum extra overhead. As an extra, it allows me to experiment with a new language (or a specific implementation like _jruby_ or _stackless_ Python) every now and then.

Links:

Project site &amp; docs: [asdf-vm.com](https://asdf-vm.github.io/asdf/).

Github [projects](https://github.com/asdf-vm/).

Lastly, I want to mention an alternative to _asdf_ I used previously called [anyenv](https://github.com/anyenv/anyenv).

Happy hacking!
